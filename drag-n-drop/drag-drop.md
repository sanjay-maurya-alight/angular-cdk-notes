# Installation
The `DragDropModule` module must be imported in the ngModule if you are using module based angular application or imported in `import` array of standalone component.

```ts
import {DragDropModule} from '@angular/cdk/drag-drop'; 
```

For example:

```ts
...
import {DragDropModule} from '@angular/cdk/drag-drop';
...
@ngModule({
    ...
    import:[DragDropModule]
    ...
})
export class AppModule{}
```

OR

```ts
...
import {DragDropModule} from '@angular/cdk/drag-drop';
...
@Component({
    ...
    standalone: true,
    import: [DragDropModule]
    ...
})
export class AppComponent{}
```

> Let's create two component to make illustration clear. We will create two standalone components: parent and child components. We will place tag of parent in root component where as child component's tag in parent's component.

```bash
$ ng g c cdk/drag-drop/parent --standalone
$ ng g c cdk/drag-drop/child --standalone
```

Place parent's tag in root component such as `AppComponent`:
```html
<!-- app.component.html -->
<app-parent><app-parent>
```

Place child's tag in parent component `ParentComponent`:
```html
<!-- parent.component.html -->
<div  class="example-box" #dragable>
    <app-child><app-child>
</div>
```

Here `#dragable` is used so that we can get instance of this HTML element

Let's style child as box by pasting below css in parent's css file

```css
/* parent.component.css */
.example-box {
    width: 200px;
    height: 200px;
    border: solid 1px #ccc;
    color: rgba(0, 0, 0, 0.87);
    cursor: move;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    background: #fff;
    border-radius: 4px;
    position: relative;
    z-index: 1;
    transition: box-shadow 200ms cubic-bezier(0, 0, 0.2, 1);
    box-shadow: 0 3px 1px -2px rgba(0, 0, 0, 0.2),
                0 2px 2px 0 rgba(0, 0, 0, 0.14),
                0 1px 5px 0 rgba(0, 0, 0, 0.12);
  }
  
  .example-box:active {
    box-shadow: 0 5px 5px -3px rgba(0, 0, 0, 0.2),
                0 8px 10px 1px rgba(0, 0, 0, 0.14),
                0 3px 14px 2px rgba(0, 0, 0, 0.12);
  }
```

#  DragDrop Service

The DragDrop service allows us to make an element draggable. By passing instance of DOM element to method `createDrag` will make that element as draggable.

Let's make child component draggble to explore various features of DragDrop service. We will be using `AfterViewInit` lifecycle hook for drag and drop instantiation and activation. In the parent's class ParentComponent, we first inject this DragDrop service to get access to class object.

```ts
// parent.component.ts
...
import {DragDrop, DragDropModule} from '@angular/cdk/drag-drop';
...
//store reference of child HTML
@ViewChild('drgable') dagger!:HTMLDivElement;

constructor(private dd:DragDrop){}
```
Next thing in the life cycle hook, we will make child component as draggle.

```ts
ngAfterViewInit(): void {
    this.dd.createDrag(this.dagger);
}
```
As soon as the code runs, the child component becomes draggable. We can drag the child component anywhere on the screen.

The `createDrag` method returns reference of draggable item as `DragRef` which we can use it to manipulate or dispose it. Let's add class property to hold `DragRef` and assign `createDrag` method's return ref:

```ts
// define property to hold drag ref
dragRef!:DragRef;

ngAfterViewInit(): void {
    this.dragRef = this.dd.createDrag(this.dagger);
}
```

## Few Methods of DragRef
Let's modify HTML for parent component html as below so that we can test the few methods after we click the button.

```html
<!-- parent.component.html -->
<div class="boundary" #boundary>
    <div class="example-box" #drgable>
        <app-child></app-child>
    </div>
</div>
<button (click)="draggedItem()">Get current details</button>
```
Here class `.boundary` is used for displaying visual boundary in which the draggble is placed and can be restricted using a method shown in example below when clicked.

```css
/* parent.component.css */
.boundary{
  border: 3px dashed gray;
  height: 300px;
  width: 400px;
}
```
And

```ts
// parent.component.ts
@ViewChild('boundary') boundary!:HTMLDivElement;
...

draggedItem()
{
    //Returns the root draggable element as HTMLElement.
    console.log('getRootElement: ', this.dragRef.getRootElement());

    //Checks whether the element is currently being dragged.
    console.log('isDragging: ', this.dragRef.isDragging());

    //Resets a standalone drag item to its initial position.
    this.dragRef.reset();

    //sets the boundary in which draggable item is restricted
    this.dragRef.withBoundaryElement(this.boundary);
}
```
Now drag the draggable item and hit the button to see console response. The method `isDragging` will always return `false` in this case as this method is test when item is being dragged.
