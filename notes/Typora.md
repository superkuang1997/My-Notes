# 标号

```css
/** initialize css counter */
#write {
    counter-reset: h1;
    max-width: 1060px;
    margin: 0 auto;
    padding: 30px;
    padding-bottom: 100px;
}

h1 {
    counter-reset: h2
}

h2 {
    counter-reset: h3
}

h3 {
    counter-reset: h4
}

h4 {
    counter-reset: h5
}

h5 {
    counter-reset: h6
}

/** put counter result into headings */
#write h1:before {
    counter-increment: h1;
    content: counter(h1) ". "
}

#write h2:before {
    counter-increment: h2;
    content: counter(h1) "." counter(h2) ". "
}

#write h3:before,
h3.md-focus.md-heading:before /** override the default style for focused headings */ {
    counter-increment: h3;
    content: counter(h1) "." counter(h2) "." counter(h3) ". "
}

#write h4:before,
h4.md-focus.md-heading:before {
    counter-increment: h4;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) ". "
}

#write h5:before,
h5.md-focus.md-heading:before {
    counter-increment: h5;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) ". "
}

#write h6:before,
h6.md-focus.md-heading:before {
    counter-increment: h6;
    content: counter(h1) "." counter(h2) "." counter(h3) "." counter(h4) "." counter(h5) "." counter(h6) ". "
}

/** override the default style for focused headings */
#write>h3.md-focus:before,
#write>h4.md-focus:before,
#write>h5.md-focus:before,
#write>h6.md-focus:before,
h3.md-focus:before,
h4.md-focus:before,
h5.md-focus:before,
h6.md-focus:before {
    color: inherit;
    border: inherit;
    border-radius: inherit;
    position: inherit;
    left:initial;
    float: none;
    top:initial;
    font-size: inherit;
    padding-left: inherit;
    padding-right: inherit;
    vertical-align: inherit;
    font-weight: inherit;
    line-height: inherit;
}
```





# 字体

base.user.css

```css
body {
    font-family: Merriweather Light, "手札体-简";
    /*font-family: Monaco, "手札体-简";*/
    color: rgb(51, 51, 51);
    line-height: 1.65;
}


/* 侧边栏 */
#typora-sidebar {
    font-family: Monaco, "手札体-简";
}

/*行内代码*/
code {
    font-family: Monaco, "手札体-简";
}

/*行间代码块*/
#write .CodeMirror-wrap .CodeMirror-code pre {
    font-family: Monaco, "手札体-简";
}


h1,
h2,
h3,
h4,
h5,
h6 {
    font-family: Merriweather Light, "方正清刻本悦宋简体";
    /*font-family: "你是我不可能的南墙";*/
    /*font-family: Merriweather Light, "你是我不可能的南墙";*/
    /*font-family: Merriweather Light, "手札体-简";*/
    /*font-family: "手札体-简", Merriweather Light;*/
    /*font-family: "方正宋刻本秀楷简体";*/
    /*font-family: Merriweather Light, "方正宋刻本秀楷简体";*/
    /*font-family: "Merriweather Light", "你是我不可能的南墙";*/
}
```

