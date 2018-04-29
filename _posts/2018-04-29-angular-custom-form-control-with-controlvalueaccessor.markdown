---
title: Angular Custom Form Control With ControlValueAccessor
date: 2018-04-29 18:37:00 +05:30
categories:
- Angular-form-api
tags:
- Angular
background: https://www.efrontlearning.com/blog/wp-content/uploads/2013/08/homepage-design-best-practices.jpg
layout: post
---

Let suppose you want to create password component in which you want an option to hide/see password on eye-like button click. We see this type of component in many sites, the common one is bitbucket login page where you can see your enter password before submitting the form.

DEMO: https://angular-fyummj.stackblitz.io

Now we start, first create component named with **password.component**after that we set up the simple hide and show logic by switching the type property of input control.

`<div class="form-group">
        <div class="input-group">
         <input [type]="!show ? 'password': 'text'"
          class="form-control" 
          id="exampleInputAmount" 
          placeholder="Enter Password" [value]="value" (input)="pushChanges($event.target.value)">
          <div class="input-group-addon"> 
	          <i class="glyphicon" 
		          [class.glyphicon-eye-open]="!show" 
		          [class.glyphicon-eye-close]="show" 
		          (click)="show = !show">
	          </i>
           </div> 
         </div>
       </div>`

That all looks easy, but now you want to use this custom component with your Angular Forms(like in reactive forms). This stage where we need ControlValueAccessor interface.

![1_wvjxZqL4ZZVGmsh3VFV2Ew.jpeg](/uploads/1_wvjxZqL4ZZVGmsh3VFV2Ew.jpeg)

In reactive form, we need to create form control explicitly or in template drive form, NgModel directive implicitly creates a form control([NgModel implementation](https://github.com/angular/angular/blob/master/packages/forms/src/directives/ng_model.ts)).Any component or directive can be turned into ControlValueAccessor by implementing ControlValueAccessor and register it with NG_VALUE_ACCESSOR provider.

Two important methods of a form control:

1. WriteValue

2. RegisterOnChange

**WriteValue** method user form control to send a value to native control.

RegisterOnChange sends a callback function that is used to notify the update value of a control. The callback function provides update value back to form control. In our currentexample, you can see **pushChange(value: any)** is used to push new value to form control API.

`
import { Component, forwardRef } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms';

export const PASWORD_VALUE_ACCESSOR: any = {
provide: NG_VALUE_ACCESSOR,
useExisting: forwardRef(() => PasswordComponent),
multi: true,
};

@Component({
selector: 'app-password',
templateUrl: './password.component.html',
styleUrls: ['./password.component.css'],
providers: [PASWORD_VALUE_ACCESSOR\]
})
export class PasswordComponent implements ControlValueAccessor {
show: boolean = false;
onChange = (_: any) => { };
onTouched = () => { };
value: string = '';
constructor() { }

writeValue(value: any): void {
this.value = value || '';
}

pushChanges(value: any) {
this.onChange(value);
}

registerOnChange(fn: (_: any) => {}): void { this.onChange = fn; }
registerOnTouched(fn: () => {}): void { this.onTouched = fn; }

setDisabledState(isDisabled: boolean): void {
}
}`

This is it, now your custom component will easily communicate with Angular form.For better understanding please refer to[this post](https://blog.angularindepth.com/never-again-be-confused-when-implementing-controlvalueaccessor-in-angular-forms-93b9eee9ee83).