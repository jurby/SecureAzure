﻿# Build and NuGet Feed Status

Package Name: CSE.SecureWebServerHelper

| Build | Release | Released Package |
|---|---|---|
|![Build status](https://dev.azure.com/flwagner/SecureAzure/_apis/build/status/SecureWebServerHelper-CI)|![Release status](https://vsrm.dev.azure.com/flwagner/_apis/public/Release/badge/1f5e9397-ffd4-41c0-843f-5a41ea0e3b86/1/1)|[![NuGet Badge](https://buildstats.info/nuget/CSE.SecureWebServerHelper?includePreReleases=true)](https://www.nuget.org/packages/CSE.SecureWebServerHelper)|

# How to use

Here is an code example with inline comments. You can use the extension functions in any combination. For a explanation on how to create a Key Vault and SSL certificate please check [here](https://github.com/h2floh/SecureAzure/blob/master/AADLogin/README.md)
```C#
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

using Microsoft.Extensions.Logging;
using Microsoft.Identity.Client;
using CSE.SecureWebServerHelper;

namespace AADLoginSamplePersonalAndAllOrg
{
    public class Program
    {

        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                // The KeyVault can be specified as parameter or in ENV 'KeyVaultName'
                // main Idea is to use Managed Identity to access the secrets in KeyVault, for non Azure Dev Environments you can specify
                // the ENV 'AzureConnectionString' with your Application ID and Secret 'RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}'
                .ConfigurationFromKeyVault()
            
                // The Azure App Service ConfigurationString is a secret and should be stored within KeyVault,
                // the configuration key where the ConfigurationString is stored can be specified as parameter 
                // if not standard value of 'ConnectionStrings:AppConfig' will be used
                .ConfigurationFromAzureAppConfig()
                
                // The KeyVault and Certificate Name can be specified as parameters or in ENV 'KeyVaultName' and ENV 'CertificateName'
                // For this example it is not needed (no SSL calls to befriended microservices)
                //.ConfigureRootCAFromKeyVault()
                
                // The KeyVault and Certificate Name can be specified as parameters or in ENV 'KeyVaultName' and ENV 'CertificateName'
                // Remark, if your RootCA differs from the SSL cert you should specify certificate name as a parameter
                .ConfigureKestrelSSLFromKeyVault()

                .UseStartup<Startup>();
    }
}
```