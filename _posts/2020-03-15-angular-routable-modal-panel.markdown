---
title: Angular Routable Modal Panel
date: 2020-03-15 22:26:00 +05:30
---

The article is all about to demonstrate how we can use the existing functionality of a router to open modal or panels in angular application. Recently I am working on a new project where I need to open floatable panels in the application.

Floatable panels on the right side of the application over existing pages. The requirement is open panels in the existing route and re-use panels in various primary routes.

The components required for creating routable modal panel are following:-

* Named Router Outlet

* Container Panel Component

* Routing Configuration for Panel Router Outlet

* Add Bootstrap Modal CSS

**Named Router Outlet**: We need to create a panel named router outlet for open floatable panel on any route. Basically we use auxiliary route to making the floatable panel available to any route in application.

Add the panel router outlet in app component to make available to all routes.

`<router-outlet name="panel"></router-outlet>`

**Panel Container** : Panel Container Component contain backdrop and router outlet for making it routable.
```
@Component({
  selector: 'app-panels',
  template: `  
    <div class="modal-backdrop fade" [class.show]="show"></div>
    <div class="modal d-block fade " [class.show]="show" tabindex="-1" role="dialog" aria-labelledby="modalIdLabel">
      <div role="document" class="modal-dialog panel-right">
        <div class="modal-content">
          <div class="container">
            <router-outlet #outlet></router-outlet>
          </div>
        </div>
      </div>
    </div>
  `
})
export class PanelsComponent implements OnInit, AfterViewInit, OnDestroy {

    show = false;
    private renderer: Renderer2;
    constructor(private renderFactory: RendererFactory2) {
    this.renderer = this.renderFactory.createRenderer(null, null);
    }

    ngOnInit() {
        this.renderer.addClass(this.body, "modal-open");
    }

    ngOnDestroy(): void {
        this.renderer.removeClass(this.body, "modal-open");
    }

    ngAfterViewInit(): void {
        setTimeout(() => (this.show = true), 100);
    }

    private get body() {
        return document.getElementsByTagName("body")[0];
    }


    close() {
        this.show = false;
        setTimeout(() => this.router.navigate([{ outlets: { panel: null } }]), 100);
    }
}
```
**Panel Routing Configuration**: Add Panel Routing configuration in router configuration.
```
export const routes: Routes = [
  {
     path: 'panel',
     component: PanelsComponent,
     outlet: 'panel',
     children: [
       {
         path: '',
         component: DemoPanelComponent
       }
     ]
  }
]
````
Register the routing configuration in RouterModule.

**Add Bootstrap Model CSS**: To provide the transition and placement of panel is done by override existing bootstrap.

```
.modal-dialog.panel-right {
	 position: fixed !important;
	 left: auto;
	 top: 0;
	 right: 0;
	 bottom: 0;
	 margin: auto !important;
	 width: 320px !important;
	 height: 100%;
	 -webkit-transform: translate3d(0%, 0, 0) !important;
	 -ms-transform: translate3d(0%, 0, 0) !important;
	 -o-transform: translate3d(0%, 0, 0) !important;
	 transform: translate3d(0%, 0, 0) !important;
}
 .modal-dialog.panel-right .modal-content {
	 height: 100%;
	 overflow-y: auto;
	 border-top-right-radius: 0px;
	 border-bottom-right-radius: 0px;
}
 .modal-dialog.panel-right .modal-body {
	 padding: 15px 15px 80px;
}
/*Right*/
 .modal.fade .modal-dialog.panel-right {
	 right: -320px;
	 -webkit-transition: opacity 0.3s linear, right 400ms ease-in-out !important;
	 -moz-transition: opacity 0.3s linear, right 400ms ease-in-out !important;
	 -o-transition: opacity 0.3s linear, right 400ms ease-in-out !important;
	 transition: opacity 0.3s linear, right 400ms ease-in-out !important;
}
 .modal.fade.show .modal-dialog.panel-right {
	 right: 0;
}
 
```

Demo Link: https://stackblitz.com/edit/router-panels


