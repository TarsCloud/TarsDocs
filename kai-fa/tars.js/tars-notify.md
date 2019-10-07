# @tars/notify

## @tars/notify

上报业务（框架）消息（告警）至 `TARS` 平台。

### report\(message\[, id\]\)

将消息上报到平台上，并可以在WEB管理页面上查看到。

* _message_: 消息内容（ **必填** ）
* _id_: 服务线程（进程）ID， _默认值为 process.pid_

### notify\(message\[, level, id\]\)

上报通知信息到平台。

* _message_: 通知内容（ **必填** ）
* _level_: 通知内容的级别，为 LEVEL 枚举， _默认值为 LEVEL.NOTIFYNORMAL_
* _id_: 服务线程（进程）ID， _默认值为 process.pid_

`LEVEL` 枚举中有3项可选：

* _LEVEL.NOTIFYNORMAL_: 正常（默认）
* _LEVEL.NOTIFYWARN_: 警告
* _LEVEL.NOTIFYERROR_: 错误

平台每10分钟对上报的异常进行收敛告警。

