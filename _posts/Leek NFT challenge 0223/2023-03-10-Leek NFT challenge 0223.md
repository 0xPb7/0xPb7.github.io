---
title: Leek NFT challenge 0223
date: 2023-03-10 09:45:47 +07:00
modified: 2023-03-10 09:24:47 +07:00
tags: [blog, json, xss, intigriti]
---


![](https://miro.medium.com/v2/resize:fit:1400/0*v2mhOVlizpRg9V4e)

**Challenge Link:** [https://challenge-0223.intigriti.io/](https://challenge-0223.intigriti.io/)

**Challenge By:** [https://twitter.com/x64pr0fessor](https://twitter.com/x64pr0fessor)

**Goal:** Now we understand that we need to find a method to display an alert box in order to confirm that this is indeed an instance of XSS.

# Reconnaissance

We were given an application that allows us to create “Leek NFT” by uploading your own picture as background. After uploading the image you have to submit. You’ll see a message “**_file uploaded successfully to 355fed1f-a3ed-44cd-b708–93e1c6f2559a”._** _Where “355fed1f-a3ed-44cd-b708–93e1c6f2559a” is some kind of UUID or other identifier of session and uploaded file.

![](https://miro.medium.com/v2/resize:fit:1400/0*Ui72KpnraowigP91)

Now click on save. You’ll be redirected to the main NFT page

![](https://miro.medium.com/v2/resize:fit:1400/0*K5NmIR5keQJcPrUa)

I don’t see any changes on the UI, which led me to access the page source to search for helpful information or JavaScript

view-source:https://challenge-0223.intigriti.io/view?viewId=355fed1f-a3ed-44cd-b708-93e1c6f2559a

![](https://miro.medium.com/v2/resize:fit:1400/0*Ut030iWIvwXfQoKq)

Upon reviewing the JavaScript code, I have realized that the application verifies specific metadata of the uploaded image, such as “`UserComment`,” “`DateTimeOriginal`,” and “`OwnerName`.” This information is retrieved directly from the image’s Exif data.

The retrieved information is added to the JSON string.

![](https://miro.medium.com/v2/resize:fit:1400/0*F0r0DX7iI6ELoIew)

The application concatenates strings without performing any sanitization, providing attackers with a potential entry point to inject any form of data into the “**`_imjobj_`**” variable.

![](https://miro.medium.com/v2/resize:fit:1400/0*KJUrlaJv4Y-T0k3n)

The **`_imgName_`** variable in the code cannot be modified because it is hardcoded, intentionally.

**Exploitation:**

With the help of the potential entry point in **“`_imgobj`_”** we could add another key named **“`imgName`”** at the end of the JSON String.

![](https://miro.medium.com/v2/resize:fit:1400/0*r0FNPniX_fl7EnVv)

Consider the below case:

```
{  
  "key1":"Naruto",  
  "key1":"Gojo"  
}
```
The final value of **`key1`** would be **`Gojo`.** Because, as mentioned above `JSON.parse()` would always take the value of the last key for multiple keys with the same name.

With the above information we could craft a payload in the **`imjobj`** JSON string

```
{  
  "imgName":"NFT.jpg",  
  "imgColorType":"02/14/2023, 20:09:16",  
  "imgComment":"0xPrashanth",  
  "imgName":"<img src=x onerror=alert(document.domain)>"  
}
```

In order to achieve the above format inject the payload in the **`imgComment`** field using **exiftool**:

```
exiftool -UserComment='pr0shx", "imgName": "<img src=x onerror=alert(document.domain)>' image.png
```

So, now upload the image.png to [_https://challenge-0223.intigriti.io/create_](https://challenge-0223.intigriti.io/create) and submit it. Click on save, you can see an **_alert_** box is popped in the challenge domain as shown below.


![](https://miro.medium.com/v2/resize:fit:1400/0*zz0jlElkVgfUp_Nv)

This attack works because the application does not sanitize user input and assume hardcoded values are immutable. Creating **`imgobj`** should be done as an object in the first place instead of creating a JSON string and parsing it to a JavaScript object.

> Prepared by Prashanth in collaboration with Pawel.

**Connect with us at -**

Twitter — [Prashanth](https://twitter.com/_0xPb), [Pawel](https://twitter.com/0xSovietBeast)
