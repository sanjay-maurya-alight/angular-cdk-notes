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
$ ng g c cdk/overlay/overlay --standalone
```

This creates component named: `OverlayComponent` with selector as `app-overlay`. We will not be using pasting this tag `<app-overlay>` anywhere. We will display it however using services and classes available in [Angular CDK](https://material.angular.io/cdk/overlay/api) documentation.


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

Next thing we need to create instance of overlay by providing configuration to `create` method. We need to use `AfterViewInit` lifecycle hook to put all our code there. Note that our UI componet is of type component and therefore type of portal we need to attach to overlay is of Component portal. The overlay can be of `ComponentPortal`, `TemplatePortal` or any HTML DOM converted to `Portal`.

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
    this.overlayRef.attach(new ComponentPortal(UiComponent));
}
```
