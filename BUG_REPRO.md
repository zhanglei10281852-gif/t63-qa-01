# Bug Reproduction

## 包的性质

当前 test_model_fix 保存的是被测模型修复后的结果源码，不是初始含 Bug 源码。要复现原始缺陷，必须检出下面固定的 parent SHA；不要在当前修复结果源码上期待重新出现修复前失败。生成系统使用的可信验证补丁和完整验证日志仅在本地留存，不提交到结果分支。

## 问题现象

驾驶员资格列表复制后，在副本中调整车型会同时改掉原驾驶员的资格信息。请修复，确保调用方处理副本不会污染已保存的驾驶员状态。

## 含 Bug 版本

- 仓库：zhanglei10281852-gif/t63-qa-01
- 仓库地址：https://github.com/zhanglei10281852-gif/t63-qa-01.git
- parent SHA：f04ade0ac31d0581a4cfb8541081734271f9f3b1

## 复现步骤

```bash
git clone -- https://github.com/zhanglei10281852-gif/t63-qa-01.git bug-repro
cd bug-repro
git checkout --detach f04ade0ac31d0581a4cfb8541081734271f9f3b1
go test ./internal/domain/crew -run TestCertificationReplacementDoesNotShareSlice -count=1
```

## 双架构完整错误信息

### linux/amd64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/domain/crew -run TestCertificationReplacementDoesNotShareSlice -count=1
--- FAIL: TestCertificationReplacementDoesNotShareSlice (0.00s)
    driver_test.go:70: clone polluted original
FAIL
FAIL	sanitation-operations/internal/domain/crew	0.003s
FAIL

```

stderr：

```text
warning: internal/domain/crew/driver_test.go has type 100755, expected 100644
warning: internal/domain/crew/driver_test.go has type 100755, expected 100644

```

### linux/arm64

- 容器内复现预期退出码：1
- 容器内复现实际退出码：1

stdout：

```text
$ go test ./internal/domain/crew -run TestCertificationReplacementDoesNotShareSlice -count=1
--- FAIL: TestCertificationReplacementDoesNotShareSlice (0.01s)
    driver_test.go:70: clone polluted original
FAIL
FAIL	sanitation-operations/internal/domain/crew	0.135s
FAIL

```

stderr：

```text
warning: internal/domain/crew/driver_test.go has type 100755, expected 100644
warning: internal/domain/crew/driver_test.go has type 100755, expected 100644

```

## 通过条件

在触发条件下，定向测试 TestCertificationReplacementDoesNotShareSlice 应通过，相关包、全量测试、竞态测试和构建检查均通过；回退 gold 唯一修复后定向测试重新失败。
