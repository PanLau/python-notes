# Mac版本破解方法：

##### 打开文件：
```
/Applications/StarUML.app/Contents/www/license/node/LicenseManagerDomain.js
```
##### 找到下面这块代码注释掉：
```
function validate(PK, name, product, licenseKey) {  
    var pk, decrypted;  
    try {  
        pk = new NodeRSA(PK);  
        decrypted = pk.decrypt(licenseKey, 'utf8');  
    } catch (err) {  
        return false;  
    }  
    var terms = decrypted.trim().split("\n");  
    if (terms[0] === name && terms[1] === product) {  
        return {   
            name: name,   
            product: product,   
            licenseType: terms[2],  
            quantity: terms[3],  
            licenseKey: licenseKey  
        };  
    } else {  
        return false;  
    }  
}  
```
##### 然后在此位置替换为新代码块:
```
function validate(PK, name, product, licenseKey) {  
        var pk, decrypted;  
        // edit by 0xcb  
        return {  
            name: "0xcb",  
            product: "StarUML",  
            licenseType: "vip",  
            quantity: "mergades.com",  
            licenseKey: "later equals never!"  
        };  
  
        try {  
            pk = new NodeRSA(PK);  
            decrypted = pk.decrypt(licenseKey, 'utf8');  
        } catch (err) {  
            return false;  
        }  
        var terms = decrypted.trim().split("\n");  
        if (terms[0] === name && terms[1] === product) {  
            return {   
                name: name,   
                product: product,   
                licenseType: terms[2],  
                quantity: terms[3],  
                licenseKey: licenseKey  
            };  
        } else {  
            return false;  
        }  
    }  
```
##### 保存此.js文件
##### 打开starUML，头部工具栏->Help->Enter License，键入内容
```
name:0xcb
licenseKey:later equals never!
```
##### 以上，谢谢！