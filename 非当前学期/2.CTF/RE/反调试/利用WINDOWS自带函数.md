在 Windows 下，存在着一些系统自带的 API，通过调用这些 API，可以检测是否正在被调试。
(1)IsDebuggerPresent()
![[Pasted image 20231217191311.png]]
(2)CheckRemoteDebuggerPresent()
![[Pasted image 20231217191335.png]]

(3)NtQueryInformationProcess()
![[Pasted image 20231217191358.png]]

## 应对策略：
`Hook` 对应的 API， 例如，对于 CheckDebug1  
而言，IsDebuggerPresent 实际直接返回 PEB 中的 BeingDebugged 字段的值，我们可以编写 Hook 函数，强制该 API 永远返回 False；对于CheckDebug3 而言我们同样可以编写 Hook 函数，对 NtQueryInformationProcess 进行 Hook，并在第二个参数为0x7时强行对第三个参数清零并返回。目前，业界已经有许多非常优秀的工具能  帮助我们自动Hook该类API，并自动绕过相当一部分的反调试，如一款  强 有 力 的 用 户 态 反 反 调 试 工 具  
ScyllaHide（https://github.com/x64dbg/ScyllaHide ） ， 能 作 为  OllyDbg、x64dbg、IDA等常用工具的插件运行，也支持独立运行。其  最新版本能够绕过VMProtect 3.x的反调试，感兴趣的读者可以自行探  索