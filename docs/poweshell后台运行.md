Start-Process -FilePath "redis-server"  -WindowStyle Hidden



Start-Process -FilePath "npm" -ArgumentList "run dev" -WindowStyle Hidden





在 PowerShell 中使用 `Start-Process -FilePath "npm" -ArgumentList "run dev" -NoNewWindow -PassThru` 启动的进程会附属于 PowerShell 窗口。如果关闭 PowerShell 窗口，所有在该窗口中启动的进程（包括你的 Node.js 应用）也会被终止。这是因为这些进程是 PowerShell 窗口的子进程。

要在关闭 PowerShell 窗口后保持 Node.js 应用程序运行，可以使用以下几种方法：

1. **使用 `Start-Process` 的 `-NoNewWindow` 参数和 `Start-Job`**：

    这种方法将命令作为后台作业启动，独立于 PowerShell 窗口运行：
    ```powershell
    Start-Job -ScriptBlock {
        Start-Process -FilePath "npm" -ArgumentList "run dev" -NoNewWindow
    }
    ```

2. **使用 `Start-Process` 的 `-WindowStyle Hidden` 参数**：

    这种方法启动一个新的进程窗口，但这个窗口在后台运行，不会关闭：
    ```powershell
    Start-Process -FilePath "npm" -ArgumentList "run dev" -WindowStyle Hidden
    ```

3. **使用 `nohup`（仅适用于 Unix-like 系统）**：

    如果你在 Unix-like 系统（如 Linux 或 macOS）上使用 PowerShell，可以使用 `nohup` 命令：
    ```powershell
    nohup npm run dev &
    ```

4. **使用 `screen` 或 `tmux`（仅适用于 Unix-like 系统）**：

    这些工具可以让你在独立的终端会话中运行命令：
    ```bash
    screen -S mynodeapp
    npm run dev
    ```

    或者：
    ```bash
    tmux new -s mynodeapp
    npm run dev
    ```

5. **使用 `Start-Process` 的 `-NoNewWindow` 参数和 `-PassThru` 参数配合 `&` 符号**：

    如果你需要在 Windows 上使用 PowerShell，可以尝试这种方法：
    ```powershell
    Start-Process -FilePath "npm" -ArgumentList "run dev" -NoNewWindow -PassThru & 
    ```

这几种方法都可以在关闭 PowerShell 窗口后继续运行 Node.js 应用程序。根据你的操作系统和需求选择合适的方法。