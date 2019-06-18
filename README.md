Windows Signtool Demo
=====================

https://stackoverflow.com/questions/84847/how-do-i-create-a-self-signed-certificate-for-code-signing-on-windows

The tools are extracted from [Microsoft Windows SDK for Windows 7 and .NET Framework 4](https://www.microsoft.com/en-us/download/details.aspx?id=8279)

``` 
makecert.exe
pvk2pfx.exe
signtool.exe
```

## Create and Sign 

You have to run any commands in Windows.

### Create certificate keys 

Note:
- All password I used in following commands are `123456`

```
makecert.exe -r -pe -n "CN=My CA" -ss CA -sr CurrentUser -a sha256 -cy authority -sky signature -sv MyCA.pvk MyCA.cer
```

### Create Code-Signing Certificate

```
makecert.exe -pe -n "CN=My SPC" -a sha256 -cy end -sky signature -ic MyCA.cer -iv MyCA.pvk -sv MySPC.pvk MySPC.cer
```

### Create PFX File

```
pvk2pfx -pvk MySPC.pvk -spc MySPC.cer -pfx MySPC.pfx
```

Note: 
- `MySPC.pfx` doesn't have password

### Sign a js file

Note:
1. Use `cmd` as `Administrator` to run following command
2. Don't use "git bash" to run the command, since it will always give error:
    `SignTool Error: No certificates were found that met all the given criteria.`
 
```
signtool sign /v /f MySPC.pfx /t http://timestamp.comodoca.com/authenticode sign.js
```

## Install to system

Not sure which one should install to root:

```
certutil -user -addstore Root MyCA.cer
```

```
certutil -user -addstore Root MySPC.cer
```

