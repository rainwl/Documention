# CSharp Open CMD exe

```C#
        string cmdPath = @"C:\Windows\System32\cmd.exe";

        // Command arguments 
        string cmdArgs = @"/c cd /D E:/flex-1.2.0 && ""C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\devenv.exe"" /Run NvFlexDemoReleaseCUDA_x64.exe";

        // Start the process
        Process process = new Process();
        process.StartInfo.FileName = cmdPath;
        process.StartInfo.Arguments = cmdArgs;
        process.Start();
```