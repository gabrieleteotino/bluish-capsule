{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080",
            "webRoot": "${workspaceFolder}"
        }
    ]
}

This is working.

{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chromium",
            "url": "http://localhost:4200",
            "runtimeExecutable": "/usr/bin/chromium",
            "webRoot": "${workspaceFolder}",
            "sourceMaps": true,
            // this enables chromium to be launched even if it is alredy open
            "userDataDir": "${workspaceRoot}/.vscode/chrome",
        }
    ]
}
