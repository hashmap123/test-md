[TOC]

#java
package users.java.com.dao;


import org.bson.types.ObjectId;
import org.mongodb.morphia.annotations.Embedded;
import org.mongodb.morphia.annotations.Entity;
import org.mongodb.morphia.annotations.Id;


import users.java.com.doc.module.userinfo;

//定义实体类存储的集合名称
@Entity(value="users",noClassnameStored=true)
public class UsersDoc {
    @Id
    ObjectId id;
    
    
    //集合中子文档-用户基本信息
	@Embedded("info")
    private  userinfo info;
    
    public  userinfo getInfo() {
        return info;
    }

    public void setInfo(userinfo info) {
        this.info = info;
    }



}
```# Disabled options

- TeX (Based on KaTeX);
- Emoji;
- Task lists;
- HTML tags decode;
- Flowchart and Sequence Diagram;

#### Editor.md directory

    editor.md/
            lib/
            css/
            scss/
            tests/
            fonts/
            images/
            plugins/
            examples/
            languages/     
            editormd.js
            ...

```html
<!-- English -->
<script src="../dist/js/languages/en.js"></script>

<!-- 繁體中文 -->
<script src="../dist/js/languages/zh-tw.js"></script>
```
