---
layout: mypost
title: VSCode添加python文件头模板
categories: [VSCode]
---

## 配置

“Preferences” —— “User Snippets” : 选择Python，生成python.json

将原来内容替换为一下内容：

```bash
{
    "HEADER":{
        "prefix": "header",
        "body": [
        "#!/usr/bin/env python",
        "# -*- encoding: utf-8 -*-",
        "'''",
        "@File    :   $TM_FILENAME",
        "@Time    :   $CURRENT_YEAR/$CURRENT_MONTH/$CURRENT_DATE $CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND",
        "@Author  :   MuYu Sen ",
        "@Version :   1.0",
        "@Contact :   ",
        "@License :   (C)Copyright 2017-2018, MuYusen",
        "@Desc    :   None",
        
        "'''",
        "",
        "# here put the import lib",
        "$0"
    ],
    }
    
}
```

其中Author改为自己的，邮箱改为自己的就可以。

在.PY文件上面输入header回车就会自动生成文件头。一般输入hea就会自动联想出来，
