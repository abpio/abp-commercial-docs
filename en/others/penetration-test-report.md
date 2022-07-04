# ABP Commercial Penetration Test Report

ABP v5.3.2 has been tested with OWASP ZAP tool. The following alerts has been reported by the tool. Many of these items are false-alert and some of them are correct alerts. In the next sections, the reason of false-alerts will be explained. For the correct alerts, there will be fixes. The issue links for the fixes will be shared in the last section.


## Alerts

There are high, medium, low and informational alerts. 

![image-20220630183012987](../images/pen-test-alert-list.png)

### Remote OS Command Injection | High

*[GET]* — https://localhost:44378/SettingManagement?AccountRecaptchaSettings.UseCaptchaOnLogin=false&AccountRecaptchaSettings.UseCaptchaOnRegistration=false&AccountSettings.EnableLocalLogin=false&AccountSettings.IsSelfRegistrationEnabled=false&AccountTwoFactorSettings.IsRememberBrowserEnabled=false&AccountTwoFactorSettings.UsersCanChange=false&BoxedLayout=true&EnableLocalLogin=true&IsRememberBrowserEnabled=true&IsSelfRegistrationEnabled=true&MenuPlacement=Left&MenuStatus=AlwaysOpened&PublicLayoutStyle=Style1&Score=0.5&SiteKey=ZAP&SiteSecret=ZAP&Style=Style6&TwoFactorBehaviour=Optional&UseCaptchaOnLogin=true%7Ctimeout+%2FT+5&UseCaptchaOnRegistration=true&UsersCanChange=true&VerifyBaseUrl=https%3A%2F%2Fwww.google.com%2F&Version=3

**Description:** Attack technique used for unauthorized execution of operating system commands. This attack is possible when an application accepts untrusted input to build operating system commands in an insecure manner involving improper data sanitization, and/or improper calling of external programs.

**Solution:** The URL param is given  `UseCaptchaOnLogin=true%7Ctimeout+%2FT+5`. As the attacker sends `true|timeout /T 15`, it should wait the request thread for 5 seconds. Because it pauses the thread. But this command is not being evaluated in the OS. Because it's being binded to a boolean property in the backend: `public bool UseCaptchaOnLogin { get; set; }` and any invalid value comes from the client will result in `false` value. To check if it's working or not, you can set it a very high number like `timeout /T 999` and you will see the request completes very fast and doesn't evaluate the OS command.

