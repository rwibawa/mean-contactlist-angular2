# MeanContactlistAngular2
[Tutorial from heroku](https://devcenter.heroku.com/articles/mean-apps-restful-api)

## 1. setup
```bash
$ ng new mean-contactlist-angular2
  create mean-contactlist-angular2/README.md (1039 bytes)
  create mean-contactlist-angular2/.angular-cli.json (1260 bytes)
  create mean-contactlist-angular2/.editorconfig (245 bytes)
  create mean-contactlist-angular2/.gitignore (516 bytes)
  create mean-contactlist-angular2/src/assets/.gitkeep (0 bytes)
  create mean-contactlist-angular2/src/environments/environment.prod.ts (51 bytes)
  create mean-contactlist-angular2/src/environments/environment.ts (387 bytes)
  create mean-contactlist-angular2/src/favicon.ico (5430 bytes)
  create mean-contactlist-angular2/src/index.html (310 bytes)
  create mean-contactlist-angular2/src/main.ts (370 bytes)
  create mean-contactlist-angular2/src/polyfills.ts (2667 bytes)
  create mean-contactlist-angular2/src/styles.css (80 bytes)
  create mean-contactlist-angular2/src/test.ts (1085 bytes)
  create mean-contactlist-angular2/src/tsconfig.app.json (211 bytes)
  create mean-contactlist-angular2/src/tsconfig.spec.json (304 bytes)
  create mean-contactlist-angular2/src/typings.d.ts (104 bytes)
  create mean-contactlist-angular2/e2e/app.e2e-spec.ts (307 bytes)
  create mean-contactlist-angular2/e2e/app.po.ts (208 bytes)
  create mean-contactlist-angular2/e2e/tsconfig.e2e.json (235 bytes)
  create mean-contactlist-angular2/karma.conf.js (923 bytes)
  create mean-contactlist-angular2/package.json (1330 bytes)
  create mean-contactlist-angular2/protractor.conf.js (722 bytes)
  create mean-contactlist-angular2/tsconfig.json (363 bytes)
  create mean-contactlist-angular2/tslint.json (2985 bytes)
  create mean-contactlist-angular2/src/app/app.module.ts (314 bytes)
  create mean-contactlist-angular2/src/app/app.component.css (0 bytes)
  create mean-contactlist-angular2/src/app/app.component.html (1120 bytes)
  create mean-contactlist-angular2/src/app/app.component.spec.ts (986 bytes)
  create mean-contactlist-angular2/src/app/app.component.ts (207 bytes)
Installing packages for tooling via npm.
Installed packages for tooling via npm.
Project 'mean-contactlist-angular2' successfully created.

$ npm install mongodb express body-parser --save
$ vi server.js
```

## 2. ng project
```bash
$ mkdir src/app/contacts

$ ng generate class contacts/contact
  create src/app/contacts/contact.ts (25 bytes)

$ ng generate component contacts/contact-details
  create src/app/contacts/contact-details/contact-details.component.css (0 bytes)
  create src/app/contacts/contact-details/contact-details.component.html (34 bytes)
  create src/app/contacts/contact-details/contact-details.component.spec.ts (685 bytes)
  create src/app/contacts/contact-details/contact-details.component.ts (304 bytes)
  update src/app/app.module.ts (439 bytes)

$ ng generate component contacts/contact-list
  create src/app/contacts/contact-list/contact-list.component.css (0 bytes)
  create src/app/contacts/contact-list/contact-list.component.html (31 bytes)
  create src/app/contacts/contact-list/contact-list.component.spec.ts (664 bytes)
  create src/app/contacts/contact-list/contact-list.component.ts (292 bytes)
  update src/app/app.module.ts (552 bytes)

$ ng generate service contacts/contact
  create src/app/contacts/contact.service.spec.ts (380 bytes)
  create src/app/contacts/contact.service.ts (113 bytes)

```

### `src/app/contacts/contact.ts`
```ts
export class Contact {
  _id?: string;
  name: string;
  email: string;
  phone: {
    mobile: string;
    work: string;
  }
}
```

### `src/app/contacts/contact.service.ts`
```ts
import { Injectable } from '@angular/core';
import { Contact } from './contact';
import { Http, Response } from '@angular/http';
import 'rxjs/add/operator/toPromise';

@Injectable()
export class ContactService {
    private contactsUrl = '/api/contacts';

    constructor (private http: Http) {}

    // get("/api/contacts")
    getContacts(): Promise<void | Contact[]> {
      return this.http.get(this.contactsUrl)
                 .toPromise()
                 .then(response => response.json() as Contact[])
                 .catch(this.handleError);
    }

    // post("/api/contacts")
    createContact(newContact: Contact): Promise<void | Contact> {
      return this.http.post(this.contactsUrl, newContact)
                 .toPromise()
                 .then(response => response.json() as Contact)
                 .catch(this.handleError);
    }

    // get("/api/contacts/:id") endpoint not used by Angular app

    // delete("/api/contacts/:id")
    deleteContact(delContactId: String): Promise<void | String> {
      return this.http.delete(this.contactsUrl + '/' + delContactId)
                 .toPromise()
                 .then(response => response.json() as String)
                 .catch(this.handleError);
    }

    // put("/api/contacts/:id")
    updateContact(putContact: Contact): Promise<void | Contact> {
      var putUrl = this.contactsUrl + '/' + putContact._id;
      return this.http.put(putUrl, putContact)
                 .toPromise()
                 .then(response => response.json() as Contact)
                 .catch(this.handleError);
    }

    private handleError (error: any) {
      let errMsg = (error.message) ? error.message :
      error.status ? `${error.status} - ${error.statusText}` : 'Server error';
      console.error(errMsg); // log to console instead
    }

}
```

## 3. Add Environment to inject the apiUrl
```bash
$ ng serve --env=local
```

### add environment variables in `src/environments/environment.local.ts`
```ts
export const environment = {
  production: false,
  apiUrl: 'http://localhost:8080'
};
```

### add it to `.angular-cli.json`
```json
{
  "$schema": "./node_modules/@angular/cli/lib/config/schema.json",
  "project": {
    "name": "mean-contactlist-angular2"
  },
  "apps": [
    {
      "environmentSource": "environments/environment.ts",
      "environments": {
        "dev": "environments/environment.ts",
        "prod": "environments/environment.prod.ts",
        "local": "environments/environment.local.ts"
      }
    }
  ]
}
```

### import it in `src/app/contacts/contact.service.ts`
```ts
import { environment } from '../../environments/environment';

@Injectable()
export class ContactService {
    private contactsUrl = environment.apiUrl + '/api/contacts';
```
