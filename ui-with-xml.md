---
nav-title: The User Interface in NativeScript
title: "UI: The Basics"
description: Learn the basic principles of designing a user interface with NativeScript. In NativeScript, you can design the UI using XML and CSS.
position: 12
---

# The User Interface

The user interface of NativeScript mobile apps consists of pages. Typically, the design of the user interface is developed and stored in `XML` files, styling is done via CSS and the business logic is developed and stored in `JavaScript` or `TypeScript` files. 

* [The Basics](#the-basics)
  * [Declare the Home Page](#declare-the-home-page)
  * [Navigate to Page](#navigate-to-page)
  * [Execute Business Logic](#execute-business-logic)
* [User Interface Components](#user-interface-components)
  * [The Default Content Components](#the-default-content-components)
    * [Page](#page)
    * [TabView](#tabview)
    * [ScrollView](#scrollview)
    * [StackLayout](#stacklayout)
    * [GridLayout](#gridlayout)
    * [WrapLayout](#wraplayout)
    * [AbsoluteLayout](#absolutelayout)
  * [Custom Components](#custom-components)
* [Bindings](#bindings)
  * [Property Binding](#property-binding)
  * [Event Binding](#event-binding)
  * [ListView Binding](#listview-binding)
  * [Expressions](#expressions)

## The Basics

When you develop the user interface of you app, you can implement each application screen in a separate page or implement your application screens on a single page with a tab view. 

For each page, you need to have a separate `XML` file which holds the layout of the page. For each `XML` file that NativeScript parses, the framework also looks for a `JavaScript` or `TypeScript` file with the same name and executes the business logic inside it.

### Declare the Home Page

Each NativeScript app must have a home page - the page that loads when you launch the app.

You need to explicitly set the home page for your app. You can do this by setting the `mainModule` member of the [`Application`](ApiReference/application/README.md) module.

When you set the `mainModule`, the NativeScript navigation framework looks for an `XML` file with the specified name, loads it and navigates to the respective page. If NativeScript discovers a `JavaScript` or `TypeScript` file with the same name, it executes the code inside it.

```JavaScript
var application = require("application");
// Set the start module for the application
application.mainModule = "app/my-page";
// Start the application
application.start();
```
```TypeScript
import application = require("application");
// Set the start module for the application
application.mainModule = "app/my-page";
// Start the application
application.start();
```

### Navigate to Page

You can navigate between pages with the `navigate` method of the [`Frame`](./ApiReference/ui/frame/Frame.md) class. The [`Frame`](ApiReference/ui/frame/Frame.md) class represents the logical unit that is responsible for navigation between different pages. Typically, each app has one frame at the root level - the topmost frame.

When you trigger navigation, NativeScript looks for an `XML` file with the specified name, loads it and navigates to the respective page. If NativeScript discovers a `JavaScript` or `TypeScript` file with the same name, it executes the code inside it.

```JavaScript
// Navigate to page called “my-page”
frames.topmost().navigate("app/my-page")
```
```TypeScript
// Navigate to page called “my-page”
frames.topmost().navigate("app/my-page")
```

> Paths are relative to the application root. In the example above, NativeScript looks for a `my-page.xml` file in the app directory of your project.

### Execute Business Logic

When you have a `JavaScript` or a `TypeScript` file in the same location with the same name as your `XML` file, NativeScript loads it together with the `XML` file. In this `JavaScript` or `TypeScript` file you can manage event handlers, bind context or execute additional business logic.

#### Example

In this example of `main-page.xml`, your page consists of a button. When you tap the button, the `buttonTap` function triggers.  

```XML
<Page>
  <StackLayout>
     <Label id="Label1" text="This is Label!" />
     <Button text="This is Button!" tap="buttonTap" />
   </StackLayout>
</Page>
```

This example app is a simple counter app. The logic for the counter is implemented in a `main-page.js` or `main-page.ts` file.

```JavaScript
var view = require("ui/core/view");
var count = 0;
function buttonTap(args) {
    count++;
    var sender = args.object;
    var parent = sender.parent;
    if (parent) {
        var lbl = view.getViewById(parent, "Label1");
        if (lbl) {
            lbl.text = "You tapped " + count + " times!";
        }
    }
}
exports.buttonTap = buttonTap;
```
```TypeScript
import observable = require("data/observable");
import view = require("ui/core/view");
import label = require("ui/label");

var count = 0;
export function buttonTap(args: observable.EventData) {
    count++;
    var sender = <view.View>args.object;
    var parent = sender.parent;
    if (parent) {
        var lbl = <label.Label>view.getViewById(parent, "Label1");
        if (lbl) {
            lbl.text = "You tapped " + count + " times!";
        }
    }
}
```

> To access variables or functions from the user interface, you need to declare them im the `exports` module.
>
> NativeScript sets each attribute value in the XML declaration to a respective property of the component. If a respective property does not exist, NativeScript sets the attribute value as an expando object.

## User Interface Components

NativeScript provides a wide range of built-in user interface components - layouts and widgets. You can also create your own custom user interface components. 

When NativeScript parses your `XML` files, it looks for components which match a name in the module exports.

For example, when you have a `Button` declaration in your `XML` file, NativeScript looks for a `Button` name in the module exports.

```JavaScript
var Button = ...
    ...
exports.Button = Button;
```

### The Default Content Components

The top-level user interface components are content components - pages and layouts. These content components let you arrange your interactive user interface components in specific ways.

#### Page

Your application pages (or screens) are instances of the [`page`](ApiReference/ui/page/Page.md) class of the [`Page`](ApiReference/ui/page/README.md) module. Typically, our app will consist of multiple application screens.

##### Example

You can execute some business login when your page loads using the `pageLoaded` event.

You need to set the `loaded` attribute for your page in your `main-page.xml`.

```XML
<Page loaded="pageLoaded">
 …
</Page>
```

You need to handle the business logic that loads in a `main-page.js` or `main-page.ts` file.

```JavaScript
function pageLoaded(args) {
    var page = args.object;
}
exports.pageLoaded = pageLoaded;
```
```TypeScript
import observable = require("data/observable");
import pages = require("ui/page");

// Event handler for Page "loaded" event attached in main-page.xml
export function pageLoaded(args: observable.EventData) {
    // Get the event sender
    var page = <pages.Page>args.object;
}
```

#### TabView

With a [`tabview`](./ApiReference/ui/tab-view/README.md), you can avoid spreading your user interface across multiple pages. Instead, you can have one page with multiple tabs.

##### Example

The following sample `main-page.xml` contains two tabs with labels.

```XML
<Page loaded="pageLoaded">
  <TabView id="tabView1">
    <TabView.items>
      <TabViewItem title="Tab 1">
        <TabViewItem.view>
          <Label text="This is Label in Tab 1" />
        </TabViewItem.view>
      </TabViewItem>
      <TabViewItem title="Tab 2">
        <TabViewItem.view>
          <Label text="This is Label in Tab 2" />
        </TabViewItem.view>
      </TabViewItem>
    </TabView.items>
  </TabView>
</Page>
```

The respective `main-page.js` or `main-page.ts` loads the first tab by its ID and shows its contents.

```JavaScript
var view = require("ui/core/view");
function pageLoaded(args) {
    var page = args.object;
    var tabView1 = view.getViewById(page, "tabView1");
    tabView1.selectedIndex = 1;
}
exports.pageLoaded = pageLoaded;
```
```TypeScript
import observable = require("data/observable");
import view = require("ui/core/view");
import pages = require("ui/page");
import tab = require("ui/tab-view");

// Event handler for Page "loaded" event attached in main-page.xml
export function pageLoaded(args: observable.EventData) {
    // Get the event sender
    var page = <pages.Page>args.object;
    var tabView1 = <tab.TabView>view.getViewById(page, "tabView1");
    tabView1.selectedIndex = 1;
}
```

#### ScrollView

You can insert a [`scrollView`](./ApiReference/ui/scroll-view/README.html) inside your page to make the page or the content enclosed in the `scrollView` scrollable.

##### Example

This sample `main-page.xml` shows how to insert a `scrollView` inside your page.

```XML
<Page>
  <ScrollView>
	…
  </ScrollView>
</Page>
```

#### StackLayout

You can arrange the user interface components in your page in a horizontal or vertical stack using [`stackLayout`](./ApiReference/ui/layouts/stack-layout/README.html).

##### Example

This sample `main-page.xml` shows how to arrange the labels in a page in a horizontal stack.

```XML
<Page>
  <StackLayout orientation="horizontal">
    <Label text="This is Label 1" />
    <Label text="This is Label 2" />
  </StackLayout>
</Page>
```

#### GridLayout

You can arrange the user interface components in your page in a flexible grid area using [`gridLayout`](./ApiReference/ui/layouts/grid-layout/README.html).

##### Example

This sample `main-page.xml` shows how to arrange labels inside a table by setting their position by row or column.

```XML
<Page>
  <GridLayout rows="*, auto" columns="250, *">
    <Label text="This is Label in row 0, col 0" />
    <Label text="This is Label in row 0, col 1" col="1" />
    <Label text="This is Label in row 1, col 0" row="1" />
    <Label text="This is Label in row 1, col 1" row="1" col="1" />
    <Label text="This is Label in row 0, col 0" rowSpan="2" colSpan="2" />
  </GridLayout>
</Page>
```

#### WrapLayout

You can arrange your user interface components in rows or columns until the space is filled and then wrap them on a new row or column using [`wrapLayout`](./ApiReference/ui/layouts/wrap-layout/README.html). By default, if orientation is not specified, `wrapLayout` arranges items horizontally.

##### Example

This sample `main-page.xml` provides four labels wrapped horizontally within the visible space of the page.

```XML
<Page>
  <WrapLayout>
    <Label text="This is Label 1" />
    <Label text="This is Label 2" />
    <Label text="This is Label 3" />
    <Label text="This is Label 4" />
  </WrapLayout>
</Page>
```

#### AbsoluteLayout

You can arrange your user interface components by left/top coordinates using [`absoluteLayout`](./ApiReference/ui/layouts/absolute-layout/README.html).

##### Example

The following `main-page.xml` contains a page with a single label positioned at the specified coordinates.

```XML
<Page>
  <AbsoluteLayout>
    <Label text="This is Label 1" left="30" top="70" />
  </AbsoluteLayout>
</Page>
```

### Custom Components

You can define your own XML namespaces to create custom user interface components.

#### Example: Code-Only Custom Component

This sample `main-page.xml` uses two custom components defined in separate declarations in the `xml-declaration` directory. The custom controls are wrapped horizontally.

```XML
<Page
    xmlns:customControls="app/xml-declaration/mymodule"
    xmlns:customOtherControls="app/xml-declaration/mymodulewithxml">
  <WrapLayout>
    <customControls:MyControl />
    <customOtherControls:MyControl />
  </WrapLayout>
</Page>
```

This sample custom component declared in `app/xml-declaration/mymodule.js` or `app/xml-declaration/mymodule.ts` exports the `MyControl` variable which creates a simple counter inside your `main-page.xml` page.

```JavaScript
var __extends = this.__extends || function (d, b) {
    for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p];
    function __() { this.constructor = d; }
    __.prototype = b.prototype;
    d.prototype = new __();
};
var stackLayout = require("ui/layouts/stack-layout");
var label = require("ui/label");
var button = require("ui/button");
var MyControl = (function (_super) {
    __extends(MyControl, _super);
    function MyControl() {
        _super.call(this);
        var counter = 0;
        var lbl = new label.Label();
        var btn = new button.Button();
        btn.text = "Tap me!";
        btn.on(button.knownEvents.tap, function (args) {
            lbl.text = "Tap " + counter++;
        });
        this.addChild(lbl);
        this.addChild(btn);
    }
    return MyControl;
})(stackLayout.StackLayout);
exports.MyControl = MyControl;
```
```TypeScript
import observable = require("data/observable");
import stackLayout = require("ui/layouts/stack-layout");
import label = require("ui/label");
import button = require("ui/button");

export class MyControl extends stackLayout.StackLayout {
    constructor() {
        super();

        var counter: number = 0;

        var lbl = new label.Label();
        var btn = new button.Button();
        btn.text = "Tap me!";
        btn.on(button.knownEvents.tap, (args: observable.EventData) => {
            lbl.text = "Tap " + counter++;
        });

        this.addChild(lbl);
        this.addChild(btn);
    }
}
```

### Example: Custom XML-Based Component 

This sample `main-page.xml` uses a custom component defined in a `app/xml-declaration/mymodulewithxml.js` or `app/xml-declaration/mymodulewithxml.ts` file. This page contains a label and button.

```XML
<Page
    xmlns:customControls="app/xml-declaration/mymodule"
    xmlns:customOtherControls="app/xml-declaration/mymodulewithxml">
  <WrapLayout>
    <customOtherControls:MyControl />
  </WrapLayout>
  <StackLayout>
    <Label id="Label1" />
    <Button text="Tap!" tap="buttonTap" />
  </StackLayout>
</Page>
```

The custom component in `app/xml-declaration/mymodulewithxml.js` or `app/xml-declaration/mymodulewithxml.ts` exports the `buttonTap` function which changes the label on every tap of the button in `main-page.xml`.

```JavaScript
var view = require("ui/core/view");
var count = 0;
function buttonTap(args) {
    count++;
    var parent = args.object.parent;
    if (parent) {
        var lbl = view.getViewById(parent, "Label1");
        if (lbl) {
            lbl.text = "You tapped " + count + " times!";
        }
    }
}
exports.buttonTap = buttonTap;
```
```TypeScript
import observable = require("data/observable");
import view = require("ui/core/view");
import label = require("ui/label");

var count = 0;
export function buttonTap(args: observable.EventData) {
    count++;

    var parent = (<view.View>args.object).parent;
    if (parent) {
        var lbl = <label.Label>view.getViewById(parent, "Label1");
        if (lbl) {
            lbl.text = "You tapped " + count + " times!";
        }
    }
}
```
## Gestures
All [UI Gestures](gestures.md) can be defined in XML. For example:
```XML
<Page>
  <Label text="Some text" tap="myTapHandler" />
</Page>
```
```JavaScript
function myTapHandler(args) {
    var context = args.view.bindingContext;
}
exports.myTapHandler = myTapHandler;
```
```TypeScript
import gestures = require("ui/gestures");

export function myTapHandler(args: gestures.GestureEventData) {
    var context = args.view.bindingContext;
}
```

## Bindings

To set a binding for a property in the `XML`, you can use double curly brackets syntax.

### Property Binding

> NativeScript looks for the custom property in the `bindingContext` of the current component or the `bindingContext` of its parents. By default, all bindings are two-way bindings.

This sample `main-page.xml` contains a simple label whose text will be populated when the page loads.

```XML
<Page loaded="pageLoaded">
{%raw%}
  <Label text="{{ name }}" />
{%endraw%}
</Page>
```

This sample `main-page.js` or `main-page.ts` file sets a `bindingContext` for the page. The `bindingContext` contains the custom property and its value. When NativeScript parses `main-page.xml`, it will populate the custom name property with the value in the `bindingContext`.

```JavaScript
function pageLoaded(args) {
	var page = args.object;

	page.bindingContext = { name: "Some name" };
}
exports.pageLoaded = pageLoaded;
```
```TypeScript
import observable = require("data/observable");
import pages = require("ui/page");

export function pageLoaded(args: observable.EventData) {
    var page = <pages.Page>args.object;
    page.bindingContext = { name: "Some name" };
}
```

### Event Binding

This sample `main-page.xml` contains a button. The text for the button and the event that the button triggers are determined when the page loads from the matching `main-page.js` or `main-page.ts` file.

```XML
<Page loaded="pageLoaded">
{%raw%}
  <Button text="{{ myProperty }}" tap="{{ myFunction }}" />
{%endraw%}
</Page>
```

This sample `main-page.js` or `main-page.ts` sets a `bindingContext` for the page. The `bindingContext` contains the custom property for the button text and its value and the custom function that will be triggered when the button is tapped. When NativeScript parses `main-page.xml`, it will populate the button text with the value in the `bindingContext` and will bind the custom function to the tap event.

```JavaScript
function pageLoaded(args) {
    var page = args.object;
    page.bindingContext = {
        myProperty: "Some text",
        myFunction: function () {
          // Your code
        }
    };
}
exports.pageLoaded = pageLoaded;
```
```TypeScript
import observable = require("data/observable");
import pages = require("ui/page");

export function pageLoaded(args: observable.EventData) {
    var page = <pages.Page>args.object;
    page.bindingContext = {
        myProperty: "Some text",
        myFunction: () => {
            // Your code
        }
    };
}
```

### ListView Binding

You can use the double curly brackets syntax to bind the items to a [`listView`](./ApiReference/ui/list-view/README.md). You can also define a template with the `itemTemplate` property from which NativeScript will create the items for your `listView`.

> Avoid accessing components by ID, especially when the component is part of a template. It is recommended to use bindings to specify component properties. 

NativeScript can create the items in a  from template when the `listView` loads inside your page. When you work with templates and a `listView`, keep in mind the scope of the `listView` and its items.

In this sample `main-page.xml`, the ListView consists of labels and each item will be created from template. The text for each label is the value of the name property for the corresponding item. 

```XML
<Page loaded="pageLoaded">
{%raw%}
  <ListView id="listView1" items="{{ myItems }}">
    <ListView.itemTemplate>
      <Label id="label1" text="{{ name }}"  />
    </ListView.itemTemplate>
  </ListView>
{%endraw%}
</Page>
```

The sample `main-page.js` or `main-page.ts` populates the `bindingContext` for the page. In this case, the code sets values for the name property for each label. Note that because the `ListView` and the Label have different scopes, you can access ListView by ID from the page but you cannot access the Label by ID. The `ListView` creates a new `Label` for every item.

```JavaScript
var view = require("ui/core/view");
function pageLoaded(args) {
    var page = args.object;
    page.bindingContext = { myItems: [{ name: "Name1" }, { name: "Name2" }, { name: "Name3" }] };

    // Will work!
    var listView1 = view.getViewById(page, "listView1");

    // Will not work!
    var label1 = view.getViewById(page, "label1");
}
exports.pageLoaded = pageLoaded;
```
```TypeScript
import observable = require("data/observable");
import pages = require("ui/page");
import view = require("ui/core/view");
import listView = require("ui/list-view");
import label = require("ui/label");

export function pageLoaded(args: observable.EventData) {
    var page = <pages.Page>args.object;
    page.bindingContext = { myItems: [{ name: "Name1" }, { name: "Name2" }, { name: "Name3" }] };

    // Will work!
    var listView1 = <listView.ListView>view.getViewById(page, "listView1");

    // Will not work!
    var label1 = <label.Label>view.getViewById(page, "label1");
}
```

### Expressions

To set an expression as the value for a property in the `XML`, you can use double curly brackets syntax.

> NativeScript reevaluates your expression on every property change of the `Observable` object set for `bindingContext`. This binding is a one-way binding - from the view model to the user interface.

The following sample `main-page.xml` shows how to set an expression as the value for a label.

```XML
{%raw%}
<Label text="{{ author ? 'by ' + author : '[no author]' }}" />
<Label text="{{ author || '[no author]' }}" />
{%endraw%}
```

**Complex Property Paths**

```JavaScript
your.sub.property[name]
```

**Logical Not operator and Comparators**

```JavaScript
!,<, >, <=, >=, ==, !=, ===, !==,||, &&
```

**Unary and Binary Operators**

```JavaScript
+, -, *, /, %
```

**Ternary Operator**

```JavaScript
a ? b : c
```

**Grouping**

```JavaScript
(a + b) * (c + d)
```

**Constants**

```JavaScript
numbers, strings, null, undefined
```