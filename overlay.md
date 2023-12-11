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
1. Using without directive
2. Using directives and corresponding handlers

We will start off with first approach first and then second approach.
