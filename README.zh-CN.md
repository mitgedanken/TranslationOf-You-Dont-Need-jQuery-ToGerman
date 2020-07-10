## You (Might) Don't Need jQuery [![Build Status](https://api.travis-ci.org/nefe/You-Dont-Need-jQuery.svg)](https://travis-ci.org/nefe/You-Dont-Need-jQuery)

前端发展很快，现代浏览器原生 API 已经足够好用。我们并不需要为了操作 DOM、Event
等再学习一下 jQuery 的 API。同时由于 React、Angular、Vue 等框架的流行，直接操作
DOM 不再是好的模式，jQuery 使用场景大大减少。本项目总结了大部分 jQuery API 替代
的方法，暂时只支持 IE10 以上浏览器。

## 目录

1. [Translations](#translations)
2. [Query Selector](#query-selector)
3. [CSS & Style](#css--style)
4. [DOM Manipulation](#dom-manipulation)
5. [Ajax](#ajax)
6. [Events](#events)
7. [Utilities](#utilities)
8. [Promises](#promises)
9. [Animation](#animation)
10. [Alternatives](#alternatives)
11. [Browser Support](#browser-support)

## Translations

- [한국어](./README.ko-KR.md)
- [正體中文](./README.zh-TW.md)
- [简体中文](./README.zh-CN.md)
- [Bahasa Melayu](./README-my.md)
- [Bahasa Indonesia](./README-id.md)
- [Português(PT-BR)](./README.pt-BR.md)
- [Tiếng Việt Nam](./README-vi.md)
- [Español](./README-es.md)
- [Русский](./README-ru.md)
- [Кыргызча](./README-kg.md)
- [Türkçe](./README-tr.md)
- [Italiano](./README-it.md)
- [Français](./README-fr.md)
- [日本語](./README-ja.md)
- [Polski](./README-pl.md)

## Query Selector

常用的 class、id、属性 选择器都可以使用 `document.querySelector` 或
`document.querySelectorAll` 替代。区别是

- `document.querySelector` 返回第一个匹配的 Element
- `document.querySelectorAll` 返回所有匹配的 Element 组成的 NodeList。它可以通过
  `[].slice.call()` 把它转成 Array
- 如果匹配不到任何 Element，jQuery 返回空数组 `[]`，但 `document.querySelector`
  返回 `null`，注意空指针异常。当找不到时，也可以使用 `||` 设置默认的值，如
  `document.querySelectorAll(selector) || []`

> 注意：`document.querySelector` 和 `document.querySelectorAll` 性能很**差**。如
> 果想提高性能，尽量使用
> `document.getElementById`、`document.getElementsByClassName` 或
> `document.getElementsByTagName`。

- [1.0](#1.0) <a name='1.0'></a> 选择器查询

  ```js
  // jQuery
  $("selector");

  // Native
  document.querySelectorAll("selector");
  ```

- [1.1](#1.1) <a name='1.1'></a> class 查询

  ```js
  // jQuery
  $(".class");

  // Native
  document.querySelectorAll(".class");

  // or
  document.getElementsByClassName("class");
  ```

- [1.2](#1.2) <a name='1.2'></a> id 查询

  ```js
  // jQuery
  $("#id");

  // Native
  document.querySelector("#id");

  // or
  document.getElementById("id");
  ```

- [1.3](#1.3) <a name='1.3'></a> 属性查询

  ```js
  // jQuery
  $("a[target=_blank]");

  // Native
  document.querySelectorAll("a[target=_blank]");
  ```

- [1.4](#1.4) <a name='1.4'></a> 后代查询

  ```js
  // jQuery
  $el.find("li");

  // Native
  el.querySelectorAll("li");
  ```

- [1.5](#1.5) <a name='1.5'></a> 兄弟及上下元�

  - 兄弟元�

    ```js
    // jQuery
    $el.siblings();

    // Native - latest, Edge13+
    [...el.parentNode.children].filter((child) => child !== el);
    // Native (alternative) - latest, Edge13+
    Array.from(el.parentNode.children).filter((child) => child !== el);
    // Native - IE10+
    Array.prototype.filter.call(
      el.parentNode.children,
      (child) => child !== el
    );
    ```

  - 上一个元�

    ```js
    // jQuery
    $el.prev();

    // Native
    el.previousElementSibling;
    ```

  - 下一个元�

    ```js
    // next
    $el.next();

    // Native
    el.nextElementSibling;
    ```

- [1.6](#1.6) <a name='1.6'></a> Closest

  Closest 获得匹配选择器的第一个祖先元素，从当前元素开始沿 DOM 树向上。

  ```js
  // jQuery
  $el.closest(queryString);

  // Native - Only latest, NO IE
  el.closest(selector);

  // Native - IE10+
  function closest(el, selector) {
    const matchesSelector =
      el.matches ||
      el.webkitMatchesSelector ||
      el.mozMatchesSelector ||
      el.msMatchesSelector;

    while (el) {
      if (matchesSelector.call(el, selector)) {
        return el;
      } else {
        el = el.parentElement;
      }
    }
    return null;
  }
  ```

- [1.7](#1.7) <a name='1.7'></a> Parents Until

  获取当前每一个匹配元素集的祖先，不包括匹配元素的本身。

  ```js
  // jQuery
  $el.parentsUntil(selector, filter);

  // Native
  function parentsUntil(el, selector, filter) {
    const result = [];
    const matchesSelector =
      el.matches ||
      el.webkitMatchesSelector ||
      el.mozMatchesSelector ||
      el.msMatchesSelector;

    // match start from parent
    el = el.parentElement;
    while (el && !matchesSelector.call(el, selector)) {
      if (!filter) {
        result.push(el);
      } else {
        if (matchesSelector.call(el, filter)) {
          result.push(el);
        }
      }
      el = el.parentElement;
    }
    return result;
  }
  ```

- [1.8](#1.8) <a name='1.8'></a> Form

  - Input/Textarea

    ```js
    // jQuery
    $("#my-input").val();

    // Native
    document.querySelector("#my-input").value;
    ```

  - 获取 e.currentTarget 在 `.radio` 中的数组索引

    ```js
    // jQuery
    $(".radio").index(e.currentTarget);

    // Native
    Array.prototype.indexOf.call(
      document.querySelectorAll(".radio"),
      e.currentTarget
    );
    ```

- [1.9](#1.9) <a name='1.9'></a> Iframe Contents

  jQuery 对象的 iframe `contents()` 返回的是 iframe 内的 `document`

  - Iframe contents

    ```js
    // jQuery
    $iframe.contents();

    // Native
    iframe.contentDocument;
    ```

  - Iframe Query

    ```js
    // jQuery
    $iframe.contents().find(".css");

    // Native
    iframe.contentDocument.querySelectorAll(".css");
    ```

- [1.10](#1.10) <a name='1.10'></a> 获取 body

  ```js
  // jQuery
  $("body");

  // Native
  document.body;
  ```

- [1.11](#1.11) <a name='1.11'></a> 获取或设置属性

  - 获取属性

    ```js
    // jQuery
    $el.attr("foo");

    // Native
    el.getAttribute("foo");
    ```

  - 设置属性

    ```js
    // jQuery, note that this works in memory without change the DOM
    $el.attr("foo", "bar");

    // Native
    el.setAttribute("foo", "bar");
    ```

  - 获取 `data-` 属性

    ```js
    // jQuery
    $el.data("foo");

    // Native (use `getAttribute`)
    el.getAttribute("data-foo");

    // Native (use `dataset` if only need to support IE 11+)
    el.dataset["foo"];
    ```

**[⬆ 回到顶部](#目录)**

## CSS & Style

- [2.1](#2.1) <a name='2.1'></a> CSS

  - Get style

    ```js
    // jQuery
    $el.css("color");

    // Native
    // 注意：此处为了解决当 style 值为 auto 时，返回 auto 的问题
    const win = el.ownerDocument.defaultView;

    // null 的意思是不返回伪类元�
    win.getComputedStyle(el, null).color;
    ```

  - Set style

    ```js
    // jQuery
    $el.css({ color: "#ff0011" });

    // Native
    el.style.color = "#ff0011";
    ```

  - Get/Set Styles

    注意，如果想一次设置多个 style，可以参考 oui-dom-utils 中
    [setStyles](https://github.com/oneuijs/oui-dom-utils/blob/master/src/index.js#L194)
    方法

  - Add class

    ```js
    // jQuery
    $el.addClass(className);

    // Native
    el.classList.add(className);
    ```

  - Remove class

    ```js
    // jQuery
    $el.removeClass(className);

    // Native
    el.classList.remove(className);
    ```

  - has class

    ```js
    // jQuery
    $el.hasClass(className);

    // Native
    el.classList.contains(className);
    ```

  - Toggle class

    ```js
    // jQuery
    $el.toggleClass(className);

    // Native
    el.classList.toggle(className);
    ```

- [2.2](#2.2) <a name='2.2'></a> Width & Height

  Width 与 Height 获取方法相同，下面以 Height 为例：

  - Window height

    ```js
    // window height
    $(window).height();

    // 不含 scrollbar，与 jQuery 行为一致
    window.document.documentElement.clientHeight;

    // 含 scrollbar
    window.innerHeight;
    ```

  - Document height

    ```js
    // jQuery
    $(document).height();

    // Native
    const body = document.body;
    const html = document.documentElement;
    const height = Math.max(
      body.offsetHeight,
      body.scrollHeight,
      html.clientHeight,
      html.offsetHeight,
      html.scrollHeight
    );
    ```

  - Element height

    ```js
    // jQuery
    $el.height();

    // Native
    function getHeight(el) {
      const styles = this.getComputedStyle(el);
      const height = el.offsetHeight;
      const borderTopWidth = parseFloat(styles.borderTopWidth);
      const borderBottomWidth = parseFloat(styles.borderBottomWidth);
      const paddingTop = parseFloat(styles.paddingTop);
      const paddingBottom = parseFloat(styles.paddingBottom);
      return (
        height - borderBottomWidth - borderTopWidth - paddingTop - paddingBottom
      );
    }

    // 精确到整数（border-box 时为 height - border 值，content-box 时为 height + padding 值）
    el.clientHeight;

    // 精确到小数（border-box 时为 height 值，content-box 时为 height + padding + border 值）
    el.getBoundingClientRect().height;
    ```

- [2.3](#2.3) <a name='2.3'></a> Position & Offset

  - Position

    获得匹配元素相对父元素的偏移

    ```js
    // jQuery
    $el.position();

    // Native
    { left: el.offsetLeft, top: el.offsetTop }
    ```

  - Offset

    获得匹配元素相对文档的偏移

    ```js
    // jQuery
    $el.offset();

    // Native
    function getOffset(el) {
      const box = el.getBoundingClientRect();

      return {
        top: box.top + window.pageYOffset - document.documentElement.clientTop,
        left:
          box.left + window.pageXOffset - document.documentElement.clientLeft,
      };
    }
    ```

- [2.4](#2.4) <a name='2.4'></a> Scroll Top

获取元素滚动条垂直位置。

```js
// jQuery
$(window).scrollTop();

// Native
(document.documentElement && document.documentElement.scrollTop) ||
  document.body.scrollTop;
```

**[⬆ 回到顶部](#目录)**

## DOM Manipulation

- [3.1](#3.1) <a name='3.1'></a> Remove

  从 DOM 中移除元素。

  ```js
  // jQuery
  $el.remove();

  // Native
  el.parentNode.removeChild(el);

  // Native - Only latest, NO IE
  el.remove();
  ```

- [3.2](#3.2) <a name='3.2'></a> Text

  - Get text

    返回指定元素及其后代的文本内容。

    ```js
    // jQuery
    $el.text();

    // Native
    el.textContent;
    ```

  - Set text

    设置元素的文本内容。

    ```js
    // jQuery
    $el.text(string);

    // Native
    el.textContent = string;
    ```

- [3.3](#3.3) <a name='3.3'></a> HTML

  - Get HTML

    ```js
    // jQuery
    $el.html();

    // Native
    el.innerHTML;
    ```

  - Set HTML

    ```js
    // jQuery
    $el.html(htmlString);

    // Native
    el.innerHTML = htmlString;
    ```

- [3.4](#3.4) <a name='3.4'></a> Append

  Append 插入到子节点的末尾

  ```js
  // jQuery
  $el.append("<div id='container'>hello</div>");

  // Native (HTML string)
  el.insertAdjacentHTML("beforeend", '<div id="container">Hello World</div>');

  // Native (Element)
  el.appendChild(newEl);
  ```

- [3.5](#3.5) <a name='3.5'></a> Prepend

  ```js
  // jQuery
  $el.prepend("<div id='container'>hello</div>");

  // Native (HTML string)
  el.insertAdjacentHTML("afterbegin", '<div id="container">Hello World</div>');

  // Native (Element)
  el.insertBefore(newEl, el.firstChild);
  ```

- [3.6](#3.6) <a name='3.6'></a> insertBefore

  在选中元素前插入新节点

  ```js
  // jQuery
  $newEl.insertBefore(queryString);

  // Native (HTML string)
  el.insertAdjacentHTML(
    "beforebegin ",
    '<div id="container">Hello World</div>'
  );

  // Native (Element)
  const el = document.querySelector(selector);
  if (el.parentNode) {
    el.parentNode.insertBefore(newEl, el);
  }
  ```

- [3.7](#3.7) <a name='3.7'></a> insertAfter

  在选中元素后插入新节点

  ```js
  // jQuery
  $newEl.insertAfter(queryString);

  // Native (HTML string)
  el.insertAdjacentHTML("afterend", '<div id="container">Hello World</div>');

  // Native (Element)
  const el = document.querySelector(selector);
  if (el.parentNode) {
    el.parentNode.insertBefore(newEl, el.nextSibling);
  }
  ```

- [3.8](#3.8) <a name='3.8'></a> is

  如果匹配给定的选择器，返回 true

  ```js
  // jQuery
  $el.is(selector);

  // Native
  el.matches(selector);
  ```

- [3.9](#3.9) <a name='3.9'></a> clone

  深拷贝被选元素。（生成被选元素的副本，包含子节点、文本和属性。）

  ```js
  //jQuery
  $el.clone();

  //Native
  //深拷贝添加参数'true'
  el.cloneNode();
  ```

- [3.10](#3.10) <a name='3.10'></a> empty

  移除所有子节点

  ```js
  //jQuery
  $el.empty();

  //Native
  el.innerHTML = "";
  ```

- [3.11](#3.11) <a name='3.11'></a> wrap

  把每个被选元素放置在指定的 HTML 结构中。

  ```js
  //jQuery
  $(".inner").wrap('<div class="wrapper"></div>');

  //Native
  Array.prototype.forEach.call(document.querySelector(".inner"), (el) => {
    const wrapper = document.createElement("div");
    wrapper.className = "wrapper";
    el.parentNode.insertBefore(wrapper, el);
    el.parentNode.removeChild(el);
    wrapper.appendChild(el);
  });
  ```

- [3.12](#3.12) <a name="3.12"></a> unwrap

  移除被选元素的父元素的 DOM 结构

  ```js
  // jQuery
  $(".inner").unwrap();

  // Native
  Array.prototype.forEach.call(document.querySelectorAll(".inner"), (el) => {
    let elParentNode = el.parentNode;

    if (elParentNode !== document.body) {
      elParentNode.parentNode.insertBefore(el, elParentNode);
      elParentNode.parentNode.removeChild(elParentNode);
    }
  });
  ```

- [3.13](#3.13) <a name="3.13"></a> replaceWith

  用指定的元素替换被选的元�

  ```js
  //jQuery
  $(".inner").replaceWith('<div class="outer"></div>');

  //Native
  Array.prototype.forEach.call(document.querySelectorAll(".inner"), (el) => {
    const outer = document.createElement("div");
    outer.className = "outer";
    el.parentNode.insertBefore(outer, el);
    el.parentNode.removeChild(el);
  });
  ```

- [3.14](#3.14) <a name='3.14'></a> simple parse

  解析 HTML/SVG/XML 字符串

  ```js
  // jQuery
  $(`<ol>
    <li>a</li>
    <li>b</li>
  </ol>
  <ol>
    <li>c</li>
    <li>d</li>
  </ol>`);

  // Native
  range = document.createRange();
  parse = range.createContextualFragment.bind(range);

  parse(`<ol>
    <li>a</li>
    <li>b</li>
  </ol>
  <ol>
    <li>c</li>
    <li>d</li>
  </ol>`);
  ```

**[⬆ 回到顶部](#目录)**

## Ajax

[Fetch API](https://fetch.spec.whatwg.org/) 是用于替换 XMLHttpRequest 处理 ajax
的新标准，Chrome 和 Firefox 均支持，旧浏览器可以使用 polyfills 提供支持。

IE9+ 请使用 [github/fetch](http://github.com/github/fetch)，IE8+ 请使用
[fetch-ie8](https://github.com/camsong/fetch-ie8/)，JSONP 请使用
[fetch-jsonp](https://github.com/camsong/fetch-jsonp)。

- [4.1](#4.1) <a name='4.1'></a> 从服务器读取数据并替换匹配元素的内容。

  ```js
  // jQuery
  $(selector).load(url, completeCallback);

  // Native
  fetch(url)
    .then((data) => data.text())
    .then((data) => {
      document.querySelector(selector).innerHTML = data;
    })
    .then(completeCallback);
  ```

**[⬆ 回到顶部](#目录)**

## Events

完整地替代命名空间和事件代理，链接到 https://github.com/oneuijs/oui-dom-events

- [5.0](#5.0) <a name='5.0'></a> Document ready by `DOMContentLoaded`

  ```js
  // jQuery
  $(document).ready(eventHandler);

  // Native
  // 检测 DOMContentLoaded 是否已完成
  if (document.readyState !== "loading") {
    eventHandler();
  } else {
    document.addEventListener("DOMContentLoaded", eventHandler);
  }
  ```

- [5.1](#5.1) <a name='5.1'></a> 使用 on 绑定事件

  ```js
  // jQuery
  $el.on(eventName, eventHandler);

  // Native
  el.addEventListener(eventName, eventHandler);
  ```

- [5.2](#5.2) <a name='5.2'></a> 使用 off 解绑事件

  ```js
  // jQuery
  $el.off(eventName, eventHandler);

  // Native
  el.removeEventListener(eventName, eventHandler);
  ```

- [5.3](#5.3) <a name='5.3'></a> Trigger

  ```js
  // jQuery
  $(el).trigger("custom-event", { key1: "data" });

  // Native
  if (window.CustomEvent) {
    const event = new CustomEvent("custom-event", { detail: { key1: "data" } });
  } else {
    const event = document.createEvent("CustomEvent");
    event.initCustomEvent("custom-event", true, true, { key1: "data" });
  }

  el.dispatchEvent(event);
  ```

**[⬆ 回到顶部](#目录)**

## Utilities

大部分实用工具都能在 native API 中找到. 其他高级功能可以选用专注于该领域的稳定性
和性能都更好的库来代替，推荐 [lodash](https://lodash.com)。

- [6.1](#6.1) <a name='6.1'></a> 基本工具

  - isArray

  检测参数是不是数组。

  ```js
  // jQuery
  $.isArray(range);

  // Native
  Array.isArray(range);
  ```

  - isWindow

  检测参数是不是 window。

  ```js
  // jQuery
  $.isWindow(obj);

  // Native
  function isWindow(obj) {
    return obj !== null && obj !== undefined && obj === obj.window;
  }
  ```

  - inArray

  在数组中搜索指定值并返回索引 (找不到则返回 -1)。

  ```js
  // jQuery
  $.inArray(item, array);

  // Native
  array.indexOf(item) > -1;

  // ES6-way
  array.includes(item);
  ```

  - isNumeric

  检测传入的参数是不是数字。 Use `typeof` to decide the type or the `type`
  example for better accuracy.

  ```js
  // jQuery
  $.isNumeric(item);

  // Native
  function isNumeric(n) {
    return !isNaN(parseFloat(n)) && isFinite(n);
  }
  ```

  - isFunction

  检测传入的参数是不是 JavaScript 函数对象。

  ```js
  // jQuery
  $.isFunction(item);

  // Native
  function isFunction(item) {
    if (typeof item === "function") {
      return true;
    }
    var type = Object.prototype.toString(item);
    return (
      type === "[object Function]" || type === "[object GeneratorFunction]"
    );
  }
  ```

  - isEmptyObject

  检测对象是否为空 (包括不可枚举属性).

  ```js
  // jQuery
  $.isEmptyObject(obj);

  // Native
  function isEmptyObject(obj) {
    return Object.keys(obj).length === 0;
  }
  ```

  - isPlainObject

  检测是不是扁平对象 (使用 “{}” 或 “new Object” 创建).

  ```js
  // jQuery
  $.isPlainObject(obj);

  // Native
  function isPlainObject(obj) {
    if (
      typeof obj !== "object" ||
      obj.nodeType ||
      (obj !== null && obj !== undefined && obj === obj.window)
    ) {
      return false;
    }

    if (
      obj.constructor &&
      !Object.prototype.hasOwnProperty.call(
        obj.constructor.prototype,
        "isPrototypeOf"
      )
    ) {
      return false;
    }

    return true;
  }
  ```

  - extend

  合并多个对象的内容到第一个对象。 object.assign 是 ES6 API，也可以使用
  [polyfill](https://github.com/ljharb/object.assign)。

  ```js
  // jQuery
  $.extend({}, defaultOpts, opts);

  // Native
  Object.assign({}, defaultOpts, opts);
  ```

  - trim

  移除字符串头尾空白。

  ```js
  // jQuery
  $.trim(string);

  // Native
  string.trim();
  ```

  - map

  将数组或对象转化为包含新内容的数组。

  ```js
  // jQuery
  $.map(array, (value, index) => {});

  // Native
  array.map((value, index) => {});
  ```

  - each

  轮询函数，可用于平滑的轮询对象和数组。

  ```js
  // jQuery
  $.each(array, (index, value) => {});

  // Native
  array.forEach((value, index) => {});
  ```

  - grep

  找到数组中符合过滤函数的元素。

  ```js
  // jQuery
  $.grep(array, (value, index) => {});

  // Native
  array.filter((value, index) => {});
  ```

  - type

  检测对象的 JavaScript [Class] 内部类型。

  ```js
  // jQuery
  $.type(obj);

  // Native
  function type(item) {
    const reTypeOf = /(?:^\[object\s(.*?)\]$)/;
    return Object.prototype.toString
      .call(item)
      .replace(reTypeOf, "$1")
      .toLowerCase();
  }
  ```

  - merge

  合并第二个数组内容到第一个数组。

  ```js
  // jQuery
  $.merge(array1, array2);

  // Native
  // 使用 concat，不能去除重复值
  function merge(...args) {
    return [].concat(...args);
  }

  // ES6，同样不能去除重复值
  array1 = [...array1, ...array2];

  // 使用 Set，可以去除重复值
  function merge(...args) {
    return Array.from(new Set([].concat(...args)));
  }
  ```

  - now

  返回当前时间的数字呈现。

  ```js
  // jQuery
  $.now();

  // Native
  Date.now();
  ```

  - proxy

  传入函数并返回一个新函数，该函数绑定指定上下文。

  ```js
  // jQuery
  $.proxy(fn, context);

  // Native
  fn.bind(context);
  ```

  - makeArray

  类数组对象转化为真正的 JavaScript 数组。

  ```js
  // jQuery
  $.makeArray(arrayLike);

  // Native
  Array.prototype.slice.call(arrayLike);

  // ES6-way
  Array.from(arrayLike);
  ```

- [6.2](#6.2) <a name='6.2'></a> 包含

  检测 DOM 元素是不是其他 DOM 元素的后代.

  ```js
  // jQuery
  $.contains(el, child);

  // Native
  el !== child && el.contains(child);
  ```

- [6.3](#6.3) <a name='6.3'></a> Globaleval

  全局执行 JavaScript 代码。

  ```js
  // jQuery
  $.globaleval(code);

  // Native
  function Globaleval(code) {
    const script = document.createElement("script");
    script.text = code;

    document.head.appendChild(script).parentNode.removeChild(script);
  }

  // Use eval, but context of eval is current, context of $.Globaleval is global.
  eval(code);
  ```

- [6.4](#6.4) <a name='6.4'></a> 解析

  - parseHTML

  解析字符串为 DOM 节点数组.

  ```js
  // jQuery
  $.parseHTML(htmlString);

  // Native
  function parseHTML(string) {
    const context = document.implementation.createHTMLDocument();

    // Set the base href for the created document so any parsed elements with URLs
    // are based on the document's URL
    const base = context.createElement("base");
    base.href = document.location.href;
    context.head.appendChild(base);

    context.body.innerHTML = string;
    return context.body.children;
  }
  ```

  - parseJSON

  传入格式正确的 JSON 字符串并返回 JavaScript 值.

  ```js
  // jQuery
  $.parseJSON(str);

  // Native
  JSON.parse(str);
  ```

**[⬆ 回到顶部](#目录)**

## Promises

Promise 代表异步操作的最终结果。jQuery 用它自己的方式处理 promises，原生
JavaScript 遵循 [Promises/A+](http://promises-aplus.github.io/promises-spec/) 标
准实现了最小 API 来处理 promises。

- [7.1](#7.1) <a name='7.1'></a> done, fail, always

  `done` 会在 promise 解决时调用，`fail` 会在 promise 拒绝时调用，`always` 总会
  调用。

  ```js
  // jQuery
  $promise.done(doneCallback).fail(failCallback).always(alwaysCallback);

  // Native
  promise.then(doneCallback, failCallback).then(alwaysCallback, alwaysCallback);
  ```

- [7.2](#7.2) <a name='7.2'></a> when

  `when` 用于处理多个 promises。当全部 promises 被解决时返回，当任一 promise 被
  拒绝时拒绝。

  ```js
  // jQuery
  $.when($promise1, $promise2).done((promise1Result, promise2Result) => {
  });

  // Native
  Promise.all([$promise1, $promise2]).then([promise1Result, promise2Result] => {});
  ```

- [7.3](#7.3) <a name='7.3'></a> Deferred

  Deferred 是创建 promises 的一种方式。

  ```js
  // jQuery
  function asyncFunc() {
    const defer = new $.Deferred();
    setTimeout(() => {
      if (true) {
        defer.resolve("some_value_computed_asynchronously");
      } else {
        defer.reject("failed");
      }
    }, 1000);

    return defer.promise();
  }

  // Native
  function asyncFunc() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (true) {
          resolve("some_value_computed_asynchronously");
        } else {
          reject("failed");
        }
      }, 1000);
    });
  }

  // Deferred way
  function defer() {
    const deferred = {};
    const promise = new Promise((resolve, reject) => {
      deferred.resolve = resolve;
      deferred.reject = reject;
    });

    deferred.promise = () => {
      return promise;
    };

    return deferred;
  }

  function asyncFunc() {
    const defer = defer();
    setTimeout(() => {
      if (true) {
        defer.resolve("some_value_computed_asynchronously");
      } else {
        defer.reject("failed");
      }
    }, 1000);

    return defer.promise();
  }
  ```

**[⬆ 回到顶部](#目录)**

## Animation

- [8.1](#8.1) <a name='8.1'></a> Show & Hide

  ```js
  // jQuery
  $el.show();
  $el.hide();

  // Native
  // 更多 show 方法的细节详见 https://github.com/oneuijs/oui-dom-utils/blob/master/src/index.js#L363
  el.style.display = "" | "inline" | "inline-block" | "inline-table" | "block";
  el.style.display = "none";
  ```

- [8.2](#8.2) <a name='8.2'></a> Toggle

  显示或隐藏元素。

  ```js
  // jQuery
  $el.toggle();

  // Native
  if (
    el.ownerDocument.defaultView.getComputedStyle(el, null).display === "none"
  ) {
    el.style.display =
      "" | "inline" | "inline-block" | "inline-table" | "block";
  } else {
    el.style.display = "none";
  }
  ```

- [8.3](#8.3) <a name='8.3'></a> FadeIn & FadeOut

  ```js
  // jQuery
  $el.fadeIn(3000);
  $el.fadeOut(3000);

  // Native
  el.style.transition = "opacity 3s";
  // fadeIn
  el.style.opacity = "1";
  // fadeOut
  el.style.opacity = "0";
  ```

- [8.4](#8.4) <a name='8.4'></a> FadeTo

  调整元素透明度。

  ```js
  // jQuery
  $el.fadeTo("slow", 0.15);
  // Native
  el.style.transition = "opacity 3s"; // 假设 'slow' 等于 3 秒
  el.style.opacity = "0.15";
  ```

- [8.5](#8.5) <a name='8.5'></a> FadeToggle

  动画调整透明度用来显示或隐藏。

  ```js
  // jQuery
  $el.fadeToggle();

  // Native
  el.style.transition = "opacity 3s";
  const { opacity } = el.ownerDocument.defaultView.getComputedStyle(el, null);
  if (opacity === "1") {
    el.style.opacity = "0";
  } else {
    el.style.opacity = "1";
  }
  ```

- [8.6](#8.6) <a name='8.6'></a> SlideUp & SlideDown

  ```js
  // jQuery
  $el.slideUp();
  $el.slideDown();

  // Native
  const originHeight = "100px";
  el.style.transition = "height 3s";
  // slideUp
  el.style.height = "0px";
  // slideDown
  el.style.height = originHeight;
  ```

- [8.7](#8.7) <a name='8.7'></a> SlideToggle

  滑动切换显示或隐藏。

  ```js
  // jQuery
  $el.slideToggle();

  // Native
  const originHeight = "100px";
  el.style.transition = "height 3s";
  const { height } = el.ownerDocument.defaultView.getComputedStyle(el, null);
  if (parseInt(height, 10) === 0) {
    el.style.height = originHeight;
  } else {
    el.style.height = "0px";
  }
  ```

- [8.8](#8.8) <a name='8.8'></a> Animate

  执行一系列 CSS 属性动画。

  ```js
  // jQuery
  $el.animate({ params }, speed);

  // Native
  el.style.transition = 'all ' + speed;
  Object.keys(params).forEach((key) =>
    el.style[key] = params[key];
  )
  ```

**[⬆ 回到顶部](#目录)**

## Alternatives

- [你可能不需要 jQuery (You Might Not Need jQuery)](http://youmightnotneedjquery.com/) -
  如何使用原生 JavaScript 实现通用事件，元素，ajax 等用法。
- [npm-dom](http://github.com/npm-dom) 以及
  [webmodules](http://github.com/webmodules) - 在 NPM 上提供独立 DOM 模块的组织

## Browser Support

| ![Chrome][chrome-image] | ![Firefox][firefox-image] | ![IE][ie-image] | ![Opera][opera-image] | ![Safari][safari-image] |
| ----------------------- | ------------------------- | --------------- | --------------------- | ----------------------- |
| Latest ✔                | Latest ✔                  | 10+ ✔           | Latest ✔              | 6.1+ ✔                  |

# License

MIT

[chrome-image]:
  https://raw.github.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png
[firefox-image]:
  https://raw.github.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png
[ie-image]:
  https://raw.github.com/alrra/browser-logos/master/src/archive/internet-explorer_9-11/internet-explorer_9-11_48x48.png
[opera-image]:
  https://raw.github.com/alrra/browser-logos/master/src/opera/opera_48x48.png
[safari-image]:
  https://raw.github.com/alrra/browser-logos/master/src/safari/safari_48x48.png
