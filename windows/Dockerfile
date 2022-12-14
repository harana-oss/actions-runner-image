ARG BASE
FROM mcr.microsoft.com/dotnet/framework/sdk:${BASE}

# latest values from https://github.com/actions/runner/releases for actions-runner-win-x64-N.N.N.zip
ARG RUNNER_VERSION=2.300.0
ARG RUNNER_DOWNLOAD_HASH=15119814c27bb0a5fc12138290fe9aa21f5df1910f2f10b06815df4f0e7f2bef

WORKDIR /actions-runner

SHELL ["powershell", "-Command", "$ErrorActionPreference = 'Stop';$ProgressPreference='silentlyContinue';"]

RUN Invoke-WebRequest -Uri "https://github.com/actions/runner/releases/download/v$env:RUNNER_VERSION/actions-runner-win-x64-$env:RUNNER_VERSION.zip" -OutFile "actions-runner-win-x64-$env:RUNNER_VERSION.zip"

RUN if((Get-FileHash -Path actions-runner-win-x64-$env:RUNNER_VERSION.zip -Algorithm SHA256).Hash.ToUpper() -ne $env:RUNNER_DOWNLOAD_HASH){ throw 'Computed checksum did not match' }

RUN Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory(""""actions-runner-win-x64-$env:RUNNER_VERSION.zip"""", $PWD)

RUN Invoke-WebRequest -Uri 'https://aka.ms/install-powershell.ps1' -OutFile install-powershell.ps1; ./install-powershell.ps1 -AddToPath

RUN powershell Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

RUN powershell choco install git.install --no-progress --params "'/GitAndUnixToolsOnPath'" -y

RUN powershell choco feature enable -n allowGlobalConfirmation

RUN powershell choco install azure-cli  --no-progress -y

CMD [ "pwsh", "-c", "./config.cmd --name $env:RUNNER_NAME --url $env:GITHUB_URL$env:RUNNER_ORG --token $env:RUNNER_TOKEN --labels $env:RUNNER_LABELS --unattended --replace --ephemeral; ./run.cmd"]
