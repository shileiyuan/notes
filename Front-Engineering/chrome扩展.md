Chrome扩展主要用于对浏览器功能的增强，它更强调与浏览器相结合。比如Chrome扩展可以在浏览器的工具栏和地址栏中显示图标，它可以更改用户当前浏览的网页中的内容，也可以更改浏览器代理服务器的设置等等。

Chrome应用更强调是独立的程序，你可以不打开Chrome浏览器而运行这些程序。同时这些程序可以调用更加底层的系统接口，比如串口、USB、本地文件读写等等。同时Chrome应用可以拥有样式更加自由的独立窗口，而Chrome扩展的界面只能限定在浏览器窗口中。

**manifest.json**

[官方模版](https://developer.chrome.com/extensions/manifest)

**browser_action**指定扩展的图标放在Chrome的工具栏中，**browser_action**中的**default_icon**属性定义了相应图标文件的位置，**default_title**定义了当用户鼠标悬停于扩展图标上所显示的文字，**default_popup**则定义了当用户单击扩展图标时所显示页面的文件位置。

```json
{
  "app": {
    "background": {
      "scripts": ["background.js"]
    }
  },
  "manifest_version": 2,
  "name": "My Extension",
  "version": "versionString",
  "default_locale": "en",
  "description": "A plain text description",
  "icons": {
    "16": "images/icon16.png",
    "48": "images/icon48.png",
    "128": "images/icon128.png"
  },
  "browser_action": {
    "default_icon": {
      "19": "images/icon19.png",
      "38": "images/icon38.png"
    },
    "default_title": "Extension Title",
    "default_popup": "popup.html"
  },
  "page_action": {
    "default_icon": {
      "19": "images/icon19.png",
      "38": "images/icon38.png"
    },
    "default_title": "Extension Title",
    "default_popup": "popup.html"
  },
  "background": {
    "scripts": ["background.js"]
  },
  "content_scripts": [
    {
      "matches": ["http://www.google.com/*"],
      "css": ["mystyles.css"],
      "js": ["jquery.js","myscript.js"]
    }
  ],
  "options_page": "options.html",
  "permissions": ["*://www.google.com/*"],
  "web_accessible_resources": ["images/*.png"]
}
```

通过Manifest中的**content_scripts**属性可以指定将哪些脚本何时注入到哪些页面中，当用户访问这些页面后，相应脚本即可自动运行，从而对页面DOM进行操作。

Google允许Chrome扩展应用不必受限于跨域限制。但出于安全考虑，需要在Manifest的**permissions**属性中声明需要跨域的权限。

**常驻后台**
在Manifest中指定background域可以使扩展常驻后台。background可以包含三种属性，分别是**scripts**、**page**和**persistent**。
如果指定了scripts属性，则Chrome会在扩展启动时自动创建一个包含所有指定脚本的页面；
如果指定了page属性，则Chrome会将指定的HTML文件作为后台页面运行。
通常我们只需要使用scripts属性即可，除非在后台页面中需要构建特殊的HTML——但一般情况下后台页面的HTML我们是看不到的。
persistent属性定义了常驻后台的方式——当其值为true时，表示扩展将一直在后台运行，无论其是否正在工作；当其值为false时，表示扩展在后台按需运行，这就是Chrome后来提出的Event Page。Event Page可以有效减小扩展对内存的消耗，如非必要，请将persistent设置为false。persistent的默认值为true。