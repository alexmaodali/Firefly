---
title: 工作流demo 
published: 2026-08-15
pinned: false
description: 工作流demo 
tags: [ffmpeg, gif]
image: "api"
category: flowable
slug: flowable-demo
---


# 工作流demo  

## 场景

你的项目有很多应用，这些应用的权限需要申请。这个申请要留痕，记录，流程需要类似OA审批，支持多级审批。

如果应用拥有不同的审批人，那么可以考虑`Expression`表达式

设计器改 demo_trigger 审批节点候选人策略为流程表达式 `${@appPermissionService.getApproverIds(appId)}`，动态获取`app_info`所绑定的审批人。`getApproverIds`需要实现具体的解析逻辑。

## 后端

核心：整个工作流交给bpm管理，业务只监听流程的状态并进行业务处理。

### 权限申请触发器

```java
public CommonResult<String> trigger() {
    // 发起流程 key=demo_trigger，businessKey 使用随机 UUID 作为业务唯一标识
    String processInstanceId = processInstanceApi.createProcessInstance(
        SecurityFrameworkUtils.getLoginUserId(),
        new BpmProcessInstanceCreateReqDTO()
        .setProcessDefinitionKey("demo_trigger")
        .setBusinessKey(UUID.randomUUID().toString())
        .setVariables(Map.of("appId", "demoApp"))).getCheckedData();
    return success(processInstanceId);
}
```

### 业务状态监听器

```java
@Slf4j
@Component
public class HkcaiDemoStatusListener extends BpmProcessInstanceStatusEventListener {

    @Resource
    private BpmDemoCallbackApi bpmDemoCallbackApi;

    @Override
    protected String getProcessDefinitionKey() {
        return "demo_trigger";
    }

    @Override
    protected void onEvent(BpmProcessInstanceStatusEvent event) {
        log.info("[onEvent][流程({}) 结束，状态({})，业务标识({})，开始回调 hkcai-server]",
                event.getProcessDefinitionKey(), event.getStatus(), event.getBusinessKey());
        bpmDemoCallbackApi.callback(BeanUtils.toBean(event, BpmDemoCallbackReqDTO.class));
    }

}
```

### 业务实现

```java
public CommonResult<Boolean> callback(BpmDemoCallbackReqDTO reqDTO) {
        // 1. 根据 businessKey（= 申请单ID）定位申请单
        Long applyId = Long.parseLong(reqDTO.getBusinessKey());
        AppPermissionApplyDO apply = appPermissionApplyMapper.selectById(applyId);
        if (apply == null) {
            log.warn("[callback][申请单({}) 不存在，流程实例({})，忽略处理]", applyId, reqDTO.getId());
            return success(false);
        }
        log.info("[callback][开始处理] 申请单({})，appId({})，申请人({})，流程实例({})，状态({})",
                applyId, apply.getAppId(), apply.getApplyUserId(), reqDTO.getId(),
                statusToDesc(reqDTO.getStatus()));

        // 2. 根据流程结束状态处理
        if (Objects.equals(reqDTO.getStatus(), BpmProcessInstanceStatusEnum.APPROVE.getStatus())) {
            // 2.1 审批通过 → 授权
            AppInfoConfigDO app = appInfoConfigService.getAppInfoConfig(apply.getAppId());
            if (app == null || app.getRoleId() == null) {
                log.error("[callback][审批通过] 应用({}) 不存在或未配置角色，无法授权，申请单({})",
                        apply.getAppId(), applyId);
                return success(false);
            }
            log.info("[callback][审批通过] 开始授权，appId({})，roleId({})，申请人({})",
                    apply.getAppId(), app.getRoleId(), apply.getApplyUserId());
            Integer count = appUserRoleService.bindUsersToRole(new AppUserRoleSaveReqVO()
                    .setRoleId(app.getRoleId())
                    .setUserIds(List.of(apply.getApplyUserId())));
            log.info("[callback][审批通过] 授权完成，绑定数量({})，申请单({})", count, applyId);
        } else if (Objects.equals(reqDTO.getStatus(), BpmProcessInstanceStatusEnum.REJECT.getStatus())
                || Objects.equals(reqDTO.getStatus(), BpmProcessInstanceStatusEnum.CANCEL.getStatus())) {
            // 2.2 审批拒绝 / 取消 → 不授权
            log.info("[callback][状态({})] 不执行授权，申请单({})",
                    statusToDesc(reqDTO.getStatus()), applyId);
        } else {
            // 2.3 其它状态
            log.warn("[callback][状态({})] 未定义的处理分支，申请单({})，流程实例({})",
                    reqDTO.getStatus(), applyId, reqDTO.getId());
        }

        // 3. 更新申请单状态（对齐 BPM 流程状态）
        appPermissionApplyMapper.updateById(apply.setStatus(reqDTO.getStatus()));
        log.info("[callback][完成] 申请单({}) 状态更新为({})", applyId, statusToDesc(reqDTO.getStatus()));
        return success(true);
    }
```

## 前端

这里的触发，可以绑定给申请权限的按钮。

```ts
// 发起 BPM 测试流程（调试用）
const handleBpmDemoTrigger = async () => {
  try {
    const processInstanceId = await triggerBpmDemo();
    message.success(`流程已发起：${processInstanceId}`);
  } catch (error) {
    console.error('发起 BPM 测试流程失败:', error);
    message.error('发起 BPM 测试流程失败');
  }
};
```

## 流程模型

测试阶段只需将节点审批人设置为自己即可。

流程设置好后，点击发布，发布流程。

![image-20260815220237819](http://imgbed.alexmaodali.dpdns.org/file/default-imgbed/1786802580878_image-20260815220237819.png)

点击通过，后台查看记录状态。 `日志见 [callback][审批通过] 授权完成`

![image-20260815220512852](http://imgbed.alexmaodali.dpdns.org/file/default-imgbed/1786802733179_image-20260815220512852.png)

## DDL

值得注意的是，你的业务需要绑定`process_instance_id`

```sql
CREATE TABLE `ai_app_permission_apply`  (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键ID',
    
  `app_id` varchar(100) NOT NULL COMMENT '申请的应用ID',
  `apply_user_id` bigint NOT NULL COMMENT '申请人ID',
  `apply_reason` varchar(500) NULL COMMENT '申请理由',
    
  `process_instance_id` varchar(64) NULL COMMENT '流程实例ID，trigger的返回流程id',

  PRIMARY KEY (`id`)
) ENGINE = InnoDB DEFAULT CHARSET = utf8mb4 COMMENT = '应用权限申请表';
```

# OA全称+各类工作流中英对照详解

## 一、OA
**OA**：Office Automation 办公自动化
释义：线上一体化办公审批管理系统，承载所有审批流转、行政办公事务

## 二、主流工作流类型 中英对照+规则+适用场景
| 中文名称 | 标准英文术语 | 流转规则 | 适用场景 |
| ---- | ---- | ---- | ---- |
| 串行审批 | Sequential Approval / Serial Approval | 按固定次序依次审批，上一级办结才流转下一位 | 日常请假、常规小额报销、入职审批 |
| 会签 | Joint Sign-off / Collective Approval | 全部审批人都必须同意，任一驳回流程作废退回 | 大额经费、正式合同、制度发文、项目立项 |
| 并行审批 | Parallel Approval | 所有审批人同步收到单据，同时处理，互不等待 | 多部门同步评审资料 |
| 或签 | Either Approval / One-of-Many Approval | 群组内任意一人审批通过，流程即可结束 | 多位分管领导任选其一批复即可的事项 |
| 抄送 | Carbon Copy (CC) | 仅阅览知悉，无审批权限，无需处理 | 审批完结后抄送财务、行政存档备案 |
| 加签 | Add Approver | 审批中途，经办人新增其他人员参与审核 | 流程需要补充专业人员复核 |
| 转签 | Transfer Approval / Delegate Approval | 本人无法处理，转交他人代为审批 | 领导出差、请假，委托下属代为处理审批 |
| 驳回 | Reject / Send Back | 审批不予通过，单据原路退回发起人修改重提 | 报销材料不全、申请不合规 |
| 撤回 | Recall / Withdraw Application | 流程未审批完毕前，发起人主动收回申请单 | 填错单据、临时取消申请 |
| 条件分支流程 | Conditional Branch Workflow | 依据金额、部门、申请类型自动匹配不同审批链路 | 分级报销：小额经理审批、大额需总经理审批 |
| 传阅流程 | Circulation Workflow | 文件逐次分发多人浏览阅读，无审批动作 | 公司新规、内部资料全员传阅学习 |
| 督办流程 | Urge / Escalation Workflow | 超时未处理自动消息提醒、升级推送上级催办 | 防止审批积压、长时间停滞 |

## 补充常用OA流程词汇
1. 发起人：Applicant / Initiator
2. 审批人：Approver
3. 流程节点：Workflow Node
4. 审批通过：Approve / Agree
5. 流程结束：Workflow Completed
6. 流程终止：Workflow Terminated


