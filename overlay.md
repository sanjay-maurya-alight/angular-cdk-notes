# Overlay

The `OverlayModule` module must be imported in the `ngModule` if you are using module based angular application or imported in `import` array of standalone component.

```ts
import {OverlayModule} from '@angular/cdk/overlay'; 
```

For example:
```ts
...
import {OverlayModule} from '@angular/cdk/overlay'; 
...
@ngModule({
    ...
    import:[OverlayModule]
    ...
})
export class AppModule{}
```

OR

```ts
...
import {OverlayModule} from '@angular/cdk/overlay'; 
...
@Component({
    ...
    standalone: true,
    import: [OverlayModule]
    ...
})
export class AppComponent{}
```

Now there are two ways we can implement Overlay.
1. Using no Overlay directives
2. Using Overlay directives and corresponding handlers

We will start off with first approach first and then second approach.

The example that we will see in this overlay note, we will be using three standalone components to demonstrate usage of directive, services and classes:
1. Main component such as default `AppComponet` as landing page
2. Portal outlet component; a container for holding overlay page
3. Simple component to display as overlay/popup

Let's create portal outlet component which will contain our logic for initializing overlay and displaying overlay programmatically.

```bash
$ ng g c cdk/overlay/outlet --standalone
```
The immediate thing we would do is to place this component in html of AppComponent so that we can see our results when application launches.

```html
<!-- app.component.html -->
<h1>Overlay Example</h1>
<app-outlet></app-outlet>
```

Next thing we need to create component that will hold our UI to be displayed as popup/overlay.

```bash
$ ng g c cdk/overlay/ui --standalone
```

This creates component named: `UIComponent` with selector as `app-ui`. We will not be using pasting this tag `<app-ui>` anywhere. We will display it however using services and classes available in [Angular CDK](https://material.angular.io/cdk/overlay/api) documentation.


Don't forget to include following CSS in the global style sheet. This is general for all angular cdk widgets.

```css
@import '@angular/cdk/overlay-prebuilt.css';
```

## Using Services and Class alone

An Overay consists of portal outlet and an UI that loads into this portal. First step in creating Overlay is to create Overlay portal with desired configuration and then attach UI to this overlay portal to display the same. The creation of overlay needs to be done in `AfterViewInit` life cycle hook.

To create overlay, we need to inject a service `Overlay` via constructor of component `OutletComponent` so that we access relevant properties and classes associated with it. The created instance is type `OverlayRef`.

```ts
//outlet.component.ts
import { Overlay } from '@angular/cdk/overlay'; 

overlayRef!:OverlayRef; //store reference of overlay

constructor(private overlay: Overlay){}
```

Next thing we need to create instance of overlay by providing configuration to `create` method. We need to use `AfterViewInit` lifecycle hook to put all our code there. Note that our UI componet is of type component and therefore type of portal we need to attach to overlay is of Component portal. The overlay can be of `ComponentPortal`, `TemplatePortal` or any custom `Portal` instance defined using HTML DOM.

```ts
ngAfterViewInit()
{
    //creating overlay reference as Portal outlet
    this.overlayRef = this.overlay.create();

    //adding UI to this outlet
    this.overlayRef.attach(new ComponentPortal(UIComponent));
}
```
As soon as we run application, we find the UIComponent is displayed to top left corner of viewport. This is default positioning of Popup or overlay and called as `GlobalPositionStrategy` strategy. In this positioning, the overlay is not related to outlet and displayed relative to viewport rather than portal outlet and we can thus create dialog or modal windows using default code.

Let's add a button in portal outlet html and handle this component attachment using a click handler.

```html
<!-- outlet.component.html -->
<button (click)="showOverlay()">Show Overlay</button>
```

Let's modify `AfterViewInit` and put attachment code to this click handler. We will then modify various positions.
```ts
ngAfterViewInit()
{
    //creating overlay reference as Portal outlet
    this.overlayRef = this.overlay.create();
}
```

```ts
//click handler
showOverlay()
{
    //adding UI to this outlet
    this.overlayRef.attach(new ComponentPortal(UIComponent));
}
```
Below is modified code that will hide the overlay when we click the button again. The button is accessible to click again when overlay is active because there is no back drop element of the overlay.

```ts
showOverlay()
{
    if (this.overlayRef.hasAttached()) {
        this.overlayRef.detach();
        return;
    }
    //adding UI to this outlet
    this.overlayRef.attach(new ComponentPortal(UIComponent));
}
```
Let's style our overlay component so that it gets displayed nice looking popup with following CSS:

```css
/* ui.component.css */
:host{
    display: block;
    width: 10em;
    height: 10em;
    background-color: #fff;
    border: 1px solid black;
    border-radius: 5px;
}
```

Next thing we will position UIComponent in the center of screen. There are two type of position strategies available: global and connected. In global position strategy the Popup is not relative to portal outlet and positioned relative to viewport. Whereas in connected strategy, the popup UI is displayed relative to portal outlet.

There are two ways in which we can set the position strategy:
1. During creation of overlay/popup using `positionStrategy` config of `create` method
2. Using `updatePositionStrategy` of reference of overlay instance.

Both these methods accept `positionStrategy` interface. The `positionStrategy` can be obtained from Overlay service already we imported. It has method `position` which returns `positionStrategy`. 

```ts
const positionStrategy = this.overlay
    .position() //get position strategy interface
    .global()   //set global position strategy
    .centerHorizontally() //align center horizontally
    .centerVertically();  //alignt center vertically
```

We can set this position strategy where ever there is demand for `positionStrategy` interface in the Angular CDK documentation. For example, in our case we can do like below:

```ts
ngAfterViewInit(): void {
    this.overlayRef = this.overlay.create({
        positionStrategy : positionStrategy //position is added here
    });
}
```
Or we can set the position strategy after we have attached the portal UI:
```ts
showOverlay()
{
    if (this.overlayRef.hasAttached()) {
        this.overlayRef.detach();
        return;
    }
    //adding UI to this outlet
    this.overlayRef.attach(new ComponentPortal(UiComponent));
    this.overlayRef.updatePositionStrategy(positionStrategy); //position is added here
}
```
There is another option to build `positionStrategy` using service `OverlayPositionBuilder`.

```ts
import { Overlay, OverlayPositionBuilder, OverlayRef } from '@angular/cdk/overlay';
constructor(private overlay:Overlay, private positionBuilder: OverlayPositionBuilder){}
```
There are two methods for this position builder:
-  `flexibleConnectedTo`
- `global`

Both these methods return `FlexibleConnectedPositionStrategy` and `GlobalPositionStrategy` instances. For example, for global position strategy:

```ts
const globalPositionStrategy = this.positionBuilder
    .global() //sets up global position strategy
    .centerHorizontally() //align center horizontally
    .centerVertically();  //alignt center vertically
```

Overlay
----------
Points to complete
- connected position
- backdrop: click, z-index, 
- directive based coding

Examples to cover
-------------------------------
Task global Position:
- pass data to modal window and display
- Click an item/action on modal; pass data from modal window to issuser
- otp screen
- create table and view information for particular column and row. For example view receipt


