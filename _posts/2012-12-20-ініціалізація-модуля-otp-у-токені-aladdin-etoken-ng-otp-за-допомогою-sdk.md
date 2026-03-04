---
layout: post
title: "Ініціалізація модуля OTP у токені \"Aladdin eToken NG-OTP\" за допомогою SDK"
date: 2012-12-20 22:59:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/12/otp-aladdin-etoken-ng-otp-sdk.html
---

### Історія

Почалася ця історія з того що компанія придбала токен "[Aladdin eToken NG-OTP](http://www.aladdin-rd.ru/catalog/etoken/otp/)" для аналізу можливості його впровадження як додаткового засобу захисту інформації.  Був час експериментування, і потім задачі відклалися. З часом я забув пароль до токену, і одним з методів відновлення його роботи була його ініціалізація (Initialize Token) засобами PKI,
наприклад "SafeNet Authentication Client". Доступ до токену я отримав, але генерація паролів OTP перстала працювати, і почала показувати помилку на дисплеї - "Err 14".  

### Теорія

"[Aladdin eToken NG-OTP](http://www.aladdin-rd.ru/catalog/etoken/otp/)" використовує для генерації одноразових паролів алгоритм HOTP котрий описаний специфікацією [IETF](http://en.wikipedia.org/wiki/IETF) [RFC 4226](http://tools.ietf.org/html/rfc4226).

"[Aladdin eToken NG-OTP](http://www.aladdin-rd.ru/catalog/etoken/otp/)" у відмінності від "[Aladdin eToken PASS](http://www.aladdin-rd.ru/catalog/etoken/pass/)", поставляється без файлу з секретним ключем ініціалізації. Хоча є бонус - секретний ключ може бути змінним.

Який ключ є зараз у токені не відомо, про це можемо прочитати у документації   
[eToken Software Developer's Guide (SDK) 4-86](http://www.aladdin-rd.ru/support/downloads/get?ID=3929&sphrase_id=105076) :  
> Функція [SDK](http://uk.wikipedia.org/wiki/SDK): "SAPI\_OTP\_GetAttributeValue" - повертає характеристики OTP об'єкту що існує. Значення ключа дуже важливі не не будуть повернуті.

Але секретний ключ ініціалізації можна згенерувати і записати до токену, і тоді він може бути відомий нам.  
> Функція [SDK](http://uk.wikipedia.org/wiki/SDK): "SAPI\_OTP\_Create" - створює об'єкт OTP.

### Вирішення проблеми

Для ініціалізації  існує програмний комплекс [SafeNet Authentication Manager (SAM)](http://www.aladdin-rd.ru/catalog/sam/) - система призначена для впровадження, управління, використання та обліку апаратних засобів аутентифікації користувачів в масштабах підприємства. Продається окремо.

  
Але для моїх потреб на етапі вивчення, цей комплекс не потрібен. Після консультації зі
службою підтримки компанії "Aladdin", з'ясував що це можна зробити за
допомогою [SDK](http://www.aladdin-rd.ru/support/downloads/get?ID=3929&sphrase_id=105076).  
І проблема є у тому що для роботи токену  потрібен об'єкт з конфігуриційними параментами OTP, котрі були видаленні у процесі ініціалізації токену.   
Тому я зробив [запит на отримання SDK](http://www.aladdin-rd.ru/support/sdk.php?sphrase_id=105076) і отримав його. На основі наданих у [SDK](http://www.aladdin-rd.ru/support/downloads/get?ID=3929&sphrase_id=105076) прикладів, створив свою програму для ініціалізації об'єкту OTP у токені - "[initOTP.exe](https://docs.google.com/open?id=0ByJHIt7DSY3cNXRkQVJUdUtMUkE)" (Win 32, x86), додатково [7-zip архів (eToken OTP by lexxai.7z)](https://drive.google.com/open?id=0ByJHIt7DSY3cejFmQzRHQ1IxSDQ).  
Програма працює у консольному режимі, тому треба запускати її з [*cmd.exe*](http://en.wikipedia.org/wiki/Command_Prompt).  
Використовувати програму "InitOTP" треба так:   
  
InitOTP <user-password> [[init counter] [hex secure key string]]   

Examples:

Random key usage: InitOTP 1234567890   
  Init count passw: InitOTP 1234567890 55

Fixed key usage : InitOTP 1234567890 0 b09cdbe8475ecca8ca22d31c798b96b6e2d831f10f0d581f

  
Де, "*user-password*" - пароль доступу до eToken,  по замовчуванні він є "1234567890".  
"*init
counter*" - початковий номер паролю OTP (0 - по замовчуванню),  
"*hex secure
key string*" - бажаний ключ, якщо його не вказувати буде його згенеровано
автоматично.   
  
Результатом роботи програми буде створено новий об'єкт OTP, з
заданим ключем або ключем згенерованим автоматично, з визначеним
початковим порядковим номером паролю OTP.  
Надалі програма запросить
натиснути клавішу генерації одноразового паролю (на токені) і таким
чином перевірить якість згенерованого пароля.  
По закінченню перевірки
програма видасть до консолі ключ у шістнадцятирічному форматі довжиною 24 байти (192 біти),
котрий треба зберегти і використовувати у програмах на стороні сервера,
наприклад у файлі - "users.otp" для "mod\_authn\_otp". Після чого токен
готовий до використання і його можна витягнути з роз'єму USB.  

### Приклади роботи

[![](/assets/images/blog/4114e25e84918127-dd05464c2f4b8ea2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhrSuM1yl6Y7bevWBFU_SvrbjuTKbVLTssVLTQiwfxr2LuAdKi2QVKUn9_KxhWNnzxnA4ZDRLTC2VT1W5OAuFzdbjqEJYn0ry_SwC37G0fpyw0gWc1t6opZWrkNCceiwT0274DJH_rPdpLu/s1600/otp-cmd-01.PNG)  
*Запуск без параметрів, показує приклади використання*

[![](/assets/images/blog/f6c2e57c1c05b1bd-74fc8c5b6ef00eed.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjf-KYOkJXo8ZXrhmEpq1FMSJgIrITItBMkwfl_ffIJ7sRtWJhPNjD_WMYbByFXc_rqPR69AcRAVvPnDymvxUzQHa2FMjQSzyQgOUM6jT0y82NTNLG389jipCvkYm_swnXcaWaeyJAHcLZB/s1600/otp-cmd-02.PNG)  
*Генерація випадкового ключа, нумерація паролів з №0*

[![](/assets/images/blog/f27927c60042dc88-147f6cac4d9a4e2e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3tDQm9V2rfwn-ek6ge1AqVEFCFZjjBKLCHz9UCwmWjN_33DMVJS7yCfqpoDl0dINhko7nAYPAUdLL2qCdHXrBZdi8ebDdQ8OdS9JczAMPMYpzgdes8ShfVOGraNfimmvHlrhmCq3Eoy3l/s1600/otp-cmd-03.PNG)  
*Генерація випадкового ключа, нумерація паролів з №2208*

[![](/assets/images/blog/37ebe81ab5f7355a-f769e4359302397f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEyN0lOC9I3UMhyphenhyphenlZjZvmh4PoCkAY96iQ8Ug6B5mS-L6HtKbhx99SFvYV2uYftjPz27zi5AZp0hIEyDF65tBjQjyAjEA8VQ9ZYNM3XLBNPgjy0YFyq9Wt9mseCbDpVgzXu7aPTMDtvjY2y/s1600/otp-cmd-04.PNG)  
*Генерація заданим ключем, нумерація паролів з №3994*

### Застосування

Таким чином знаючи секретний ключ ініціалізації можна використовувати Aladdin eToken NG-OTP у своїх реалізаціях, використовуючи, наприклад як [PHP модуль](http://habrahabr.ru/post/105268/) , або у [RADIUS](http://en.wikipedia.org/wiki/Radius_server) сервері, або модуль сервера [Apache - mod\_authn\_otp](https://lexxai.github.io/2012/12/otp-apache-modauthnotp.html), чи для [Модулів аутентифікації - PAM.](http://en.wikipedia.org/wiki/Pluggable_Authentication_Modules)  
  

* #### Застосування з використанням PHP

> Використати дописану мною [програму](https://docs.google.com/open?id=0ByJHIt7DSY3cNUNoaXUtS2UtajQ) на PHP, на основі [цієї публікації](http://habrahabr.ru/post/105268).
> Програма запитує у користувача два послідовно отриманих від токену
> паролі, знаючи ключ токену (прописаний у тілі програми), методом
> перебору визначає який поточний номер паролю. Ця програм також може
> використовується для автентифікації введеного одноразового паролю.   
> Використання:  
> php etoken.php pass=894206   
> result 894206 is count :16  
>   
> php etoken.php pass=294306 pass1=357914  
> Sync password ok. Result - next user count will be :24

* #### Застосування з використанням mod\_authn\_otp

> [Одноразові паролі (OTP) та сервер Apache (mod\_authn\_otp)](https://lexxai.github.io/2012/12/otp-apache-modauthnotp.html)

### Додаток

З початковим код програми на С++ можете ознайомитися завантажив її : [initOTP.сpp](https://docs.google.com/open?id=0ByJHIt7DSY3cbkJsZERwQVpWZGs)  
  
 initOTP.cpp:  

```
/////////////////////////////////////////////////////////////////////////////
// eToken SDK Sample
// 
//  LICENSE AGREEMENT:
//  1. COPYRIGHTS AND TRADEMARKS
//  The eTokenTM system and its documentation are copyright (C) 1985 to present,
//  by Aladdin Knowledge Systems Ltd. All rights reserved.
//
//  eToken is a trademark and ALADDIN KNOWLEDGE SYSTEMS LTD is a registered trademark 
//  of Aladdin Knowledge Systems Ltd. All  other  trademarks,  brands,  and product 
//  names used in this guide are trademarks of their respective owners.
//
//  2. Title & Ownership
//  THIS IS A LICENSE AGREEMENT AND NOT AN AGREEMENT FOR SALE. 
//  The Code IS NOT FOR SALE and is and shall remain as Aladdin's sole property. 
//  All right, title and interest in and to the Code, including associated 
//  intellectual property rights, in and to the Code are and will remain with Aladdin.
//
//  3.   Disclaimer of Warranty
//  THE CODE CONSTITUTES A CODE SAMPLE AND IS NOT A COMPLETE PRODUCT AND MAY CONTAIN 
//  DEFECTS, AND PRODUCE UNINTENDED OR ERRONEOUS RESULTS. THE CODE IS PROVIDED "AS IS", 
//  WITHOUT WARRANTY OF ANY KIND. ALADDIN DISCLAIMS ALL WARRANTIES, EITHER EXPRESS OR 
//  IMPLIED, INCLUDING BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY 
//  AND FITNESS FOR A PARTICULAR PURPOSE.
//  The entire risk arising out of the use or performance of the Code remains with you.
//
//  4.   No Liability For Damages
//  Without derogating from the above, in no event shall Aladdin be liable for any damages 
//  whatsoever (including, without limitation, damages for loss of business profits, business 
//  interruption, loss of business information, or other pecuniary loss) arising out of the 
//  use of or inability to use the Code, even if Aladdin has been advised of the possibility 
//  of such damages. Your sole recourse in the event of any dissatisfaction with the Code is 
//  to stop using it and return it.
/////////////////////////////////////////////////////////////////////////////
//
//   adopted by lexxai, http://lexxai.blogspot.com
//
/////////////////////////////////////////////////////////////////////////////

#include <stdio.h>
#include <stdlib.h>
#ifdef _WIN32#include <windows.h>
#else
#include "wintypes.h"
#include <string.h>
#include <dlfcn.h>
#include <unistd.h>
#endif
#include "eTPkcs11.h"
#include "eTSAPI.h"
#ifdef _WIN32#define PKCS11_DLL_NAME         "etpkcs11.dll"
#define ETSAPI_DLL_NAME         "etsapi.dll"
#else
#define PKCS11_DLL_NAME         "libeTPkcs11.so"
#define ETSAPI_DLL_NAME         "libeTSapi.so"
#define LoadLibrary(lib)        dlopen(lib,RTLD_NOW)
#define GetProcAddress          dlsym#define FreeLibrary(lib)        dlclose(lib)
typedef void * HINSTANCE;
#endif//_WIN32  
// Print message and stop execution.
static void leave(const char * message)
{
        if (message) printf("%s\n", message);
        printf("Press the Enter key to exit");
        getchar();
        exit(message ? -1 : 0);
}

CK_FUNCTION_LIST_PTR fl = NULL; // PKCS#11 functions list.
// Load eTPKCS11.dll and initialize PKCS#11.
void LoadPKCS11()
{
        HINSTANCE hLib = LoadLibrary(PKCS11_DLL_NAME); 
        if (!hLib) leave("Cannot load PKCS11");

        CK_C_GetFunctionList f_C_GetFunctionList = NULL;
#ifdef _WIN32
        (FARPROC&)f_C_GetFunctionList= GetProcAddress(hLib, "C_GetFunctionList");
#else
        *(void**)&f_C_GetFunctionList= GetProcAddress(hLib, "C_GetFunctionList");
#endif  
        if (!f_C_GetFunctionList) leave("C_GetFunctionList not found");

        if (CKR_OK != f_C_GetFunctionList(&fl)) leave("C_GetFunctionList failed");
        if (CKR_OK != fl->C_Initialize(0)) leave("C_Initialize failed");
}
// SAPI functions declaration.
typedef CK_RV (*t_SAPI_OTP_Create)(
        CK_SESSION_HANDLE hSession, 
        CK_ATTRIBUTE_PTR pTemplate, 
        CK_ULONG ulCount);
typedef CK_RV (*t_SAPI_OTP_Destroy)(
        CK_SESSION_HANDLE hSession);
typedef CK_RV (*t_SAPI_OTP_Execute)(
        CK_SESSION_HANDLE hSession, 
        CK_ULONG mode, 
        CK_CHAR_PTR pResult, 
        CK_ULONG_PTR pSize);
typedef CK_RV (*t_SAPI_OTP_GetMechanismInfo)(
        CK_SLOT_ID slotId, 
        CK_ULONG mechanism, 
        CK_SAPI_OTP_MECHANISM_INFO_PTR pMechanismInfo);
typedef CK_RV (*t_SAPI_Server_OTP_Calculate)(
        CK_ATTRIBUTE_PTR pTemplate, 
        CK_ULONG ulCount, 
        CK_CHAR_PTR pResult, 
        CK_ULONG_PTR pSize);

t_SAPI_OTP_Create _SAPI_OTP_Create = NULL;
t_SAPI_OTP_Destroy _SAPI_OTP_Destroy = NULL;
t_SAPI_OTP_Execute _SAPI_OTP_Execute = NULL;
t_SAPI_OTP_GetMechanismInfo _SAPI_OTP_GetMechanismInfo = NULL;
t_SAPI_Server_OTP_Calculate _SAPI_Server_OTP_Calculate = NULL;
// Load eTSAPI.dll and acquire its usable functions.
void LoadETSAPI()
{
        HINSTANCE hLib = LoadLibrary(ETSAPI_DLL_NAME); 
        if (!hLib) leave("Cannot load etsapi");
#ifdef _WIN32
        (FARPROC&)_SAPI_OTP_Create = GetProcAddress(hLib, "SAPI_OTP_Create");
#else
        *(void**)&_SAPI_OTP_Create = GetProcAddress(hLib, "SAPI_OTP_Create");
#endif
        if (!_SAPI_OTP_Create) leave("SAPI_OTP_Create not found");
#ifdef _WIN32
        (FARPROC&)_SAPI_OTP_Destroy = GetProcAddress(hLib, "SAPI_OTP_Destroy");
#else
        *(void**)&_SAPI_OTP_Destroy = GetProcAddress(hLib, "SAPI_OTP_Destroy");
#endif
        if (!_SAPI_OTP_Destroy) leave("SAPI_OTP_Delete not found");
#ifdef _WIN32
        (FARPROC&)_SAPI_OTP_Execute = GetProcAddress(hLib, "SAPI_OTP_Execute");
#else
        *(void**)&_SAPI_OTP_Execute = GetProcAddress(hLib, "SAPI_OTP_Execute");
#endif
        if (!_SAPI_OTP_Execute) leave("SAPI_OTP_Execute not found");
#ifdef _WIN32
        (FARPROC&)_SAPI_OTP_GetMechanismInfo = GetProcAddress(hLib, "SAPI_OTP_GetMechanismInfo");
#else
        *(void**)&_SAPI_OTP_GetMechanismInfo = GetProcAddress(hLib, "SAPI_OTP_GetMechanismInfo");
#endif
        if (!_SAPI_OTP_GetMechanismInfo) leave("SAPI_OTP_GetMechanismInfo not found");
#ifdef _WIN32
        (FARPROC&)_SAPI_Server_OTP_Calculate = GetProcAddress(hLib, "SAPI_Server_OTP_Calculate");
#else
        *(void**)&_SAPI_Server_OTP_Calculate = GetProcAddress(hLib, "SAPI_Server_OTP_Calculate");
#endif
        if (!_SAPI_Server_OTP_Calculate) leave("SAPI_Server_OTP_Calculate not found");
}
// Locate an inserted eToken.
CK_SLOT_ID LocateToken()
{
        CK_ULONG nSlots = 1;
        CK_SLOT_ID nSlotID;
        if (CKR_OK != fl->C_GetSlotList(TRUE, &nSlotID, &nSlots)) leave("C_GetSlotList failed");
        if (nSlots<1) leave("No eToken inserted");
        return nSlotID;
}
int main(int argc, char* argv[])
{
        LoadPKCS11();
        LoadETSAPI();

        CK_SLOT_ID nSlotID = LocateToken();

        // Open PKCS#11 session.
        CK_SESSION_HANDLE hSession;
        CK_RV rv = fl->C_OpenSession(nSlotID, CKF_RW_SESSION|CKF_SERIAL_SESSION, NULL, NULL, &hSession);
        if (rv!=0) leave("C_OpenSession failed");

        if(argc < 2)
        {
                leave("Usage:\n"
                        " InitOTP <user-password> [[init counter] [hex secure key string]]\n"
                        "  Examples:\n   Random key usage: InitOTP 1234567890\n"
                        "   Init count passw: InitOTP 1234567890 55\n"
                        "   Fixed key usage : InitOTP 1234567890 0 b09cdbe8475ecca8ca22d31c798b96b6e2d831f10f0d581f\n"
                        );
        }

        const char* Pass = argv[1];
        CK_ULONG initcounter;
        initcounter=0;
        const char *SecureKey = argv[3], *pos=SecureKey;
        // Perform PKCS#11 login.
        rv = fl->C_Login(hSession, CKU_USER, (CK_CHAR*) Pass, 10);
        if (rv!=0) leave("\nError. user-password is wrong for this token\n");

        printf("Clean OTP object\n");
        _SAPI_OTP_Destroy(hSession); // Clean up.

        printf("Create OTP object\n");
        
        if (argc > 2) 
        {
                initcounter=atoi(argv[2]);
        }
        printf("\nInit counter is %li\n\n",initcounter);

        BYTE key[24];
        memset (key,0,sizeof(key)); // default fill array of key by 0x00
        if (argc > 3)
        {
                //Fixed key value used from user
                //detect wrong length of key string
                if (!(strlen(SecureKey)>0))
                {
                        leave("\nError. Enter correct secure key");
                }
                if (strlen(SecureKey)>48)
                {
                        printf("\nWarning. For secure key will be used only first 24 bytes\n");
                }
                //Convert hex string to byte array
                while( *pos )
                {
                        if( !((pos-SecureKey)&1) )
                                sscanf_s(pos,"%02x",&key[(pos-SecureKey)>>1]);
                        ++pos;
                }
                printf("Used fixed key with length:%d bytes\n\n",sizeof(key));
        }
        else
        {
                // Generate random key value
                //JV Card OTP MinKeySize = 20, MaxKeySize = 24
                //CardOS OTP MinKeySize = 20, MaxKeySize = 32.
                rv = fl->C_GenerateRandom(hSession, key, sizeof(key));
                if (rv!=0) leave("C_GenerateRandom failed");
                printf("Generated radnom key with length:%d bytes\n\n",sizeof(key));
        }

        for (CK_ULONG counter = 0; counter<sizeof(key); counter++) 
        {
                printf("%02x",(const char *) (const char *) key[counter]);
        }
        printf("\n\n");



        // Create new OTP object.
        CK_ULONG mech = CK_SAPI_OTP_HMAC_SHA1_DEC6;
        CK_BBOOL ck_false = FALSE;
        CK_ATTRIBUTE tCreate[] = {
                {CKA_SAPI_OTP_MECHANISM,    &mech,        sizeof(CK_ULONG)}, 
                {CKA_SAPI_OTP_VALUE,        key,          sizeof(key)     },
                {CKA_SAPI_OTP_NEXT_ALLOWED, &ck_false,     sizeof(CK_BBOOL)},
                {CKA_SAPI_OTP_COUNTER,      &initcounter,     sizeof(CK_ULONG)},
        };
        rv = _SAPI_OTP_Create(hSession, tCreate, sizeof(tCreate)/sizeof(CK_ATTRIBUTE));
        if (rv) leave("SAPI_OTP_Create failed");

        // Check if eToken supports the OTP button in connected mode.
        CK_SAPI_OTP_MECHANISM_INFO mi;
        rv = _SAPI_OTP_GetMechanismInfo(nSlotID, CK_SAPI_OTP_HMAC_SHA1_DEC6, &mi);
        if (rv) leave("SAPI_OTP_GetMechanismInfo failed");
        if ((mi.flags & CK_SAPI_OTP_BUTTON_SUPPORTED)==0)
        {
                leave("This token does not support the OTP button in connected mode");
        }

        // Get current OTP value.
        printf("Acquire current OTP\n");
        CK_CHAR OTP_current[7];
        CK_ULONG OTPLen_current = sizeof(OTP_current);
        rv = _SAPI_OTP_Execute(hSession, CK_SAPI_OTP_CURRENT, OTP_current, &OTPLen_current);
        if (rv) leave("SAPI_OTP_Execute failed");

        printf("Acquire next OTP. Press eToken button to generate next OTP value.\n");
        CK_CHAR OTP[7];
        CK_ULONG OTPLen = sizeof(OTP);
        BOOL bChanged = FALSE;
        while (!bChanged)
        {
                // Get current OTP value.
#ifdef _WIN32
                Sleep(100);
#else
                usleep(100);
#endif
                rv = _SAPI_OTP_Execute(hSession, CK_SAPI_OTP_CURRENT, OTP, &OTPLen);
                if (rv) leave("SAPI_OTP_Execute failed");
                bChanged = (OTPLen_current!=OTPLen) || (memcmp(OTP_current, OTP, OTPLen)); 
        }

        rv = _SAPI_OTP_Execute(hSession, CK_SAPI_OTP_RELEASE, OTP, &OTPLen);

        printf("Calculate OTP on the server side\n");
        BOOL bSuccess = FALSE;
        CK_ULONG coun;
        CK_ULONG window;
        window=5000;

        for (CK_ULONG counter = 0; !bSuccess && counter<window; counter++) // Maximum of 5000 tries on the server side.
        {
                CK_CHAR Result[7];
                CK_ULONG ResultLen = sizeof(Result);

                // OTP object template.
                CK_ATTRIBUTE tExecute[] = {
                        {CKA_SAPI_OTP_MECHANISM,    &mech,        sizeof(CK_ULONG)}, 
                        {CKA_SAPI_OTP_VALUE,        key,          sizeof(key)     },
                        {CKA_SAPI_OTP_COUNTER,      &counter,     sizeof(CK_ULONG)},
                };

                // Server side calculation.
                rv = _SAPI_Server_OTP_Calculate(tExecute, sizeof(tExecute)/sizeof(CK_ATTRIBUTE), Result, &ResultLen);
                if (rv) leave("SAPI_Server_OTP_Calculate failed\n");

                bSuccess = (ResultLen==OTPLen) && (!memcmp(Result, OTP, OTPLen)); // Check result.
                coun=counter;
        }

        if (!bSuccess) printf("Client/Server OTP mismatch, or Init counter more than %i\n",window);
        else printf("OTP was successfully verified on the server side, key number: %i \n\n", coun);

        //f_SAPI_OTP_Destroy(hSession);
        fl->C_Finalize(0);
        leave(NULL);
        return 0;
}
```

 (c) 2012. lexxai  
  
P.S.  На сьогодні, 2019 рік, використовуючи Windows 10, та SafeNetAuthenticationClient-x32-x64-10.4, програма не працює за відсутністю бібліотеки.  

```
#define ETSAPI_DLL_NAME         "etsapi.dll"
```
