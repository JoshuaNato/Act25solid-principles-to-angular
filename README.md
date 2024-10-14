## S - Single Responsibility Principle:

Applying SOLID Principles to Angular with examples https://sheldonrcohen.medium.com/applying-solid-principles-to-angular-with-examples-fec460ffa541

The acronym SOLID stands for:

S: Single Responsibility Principle
O: Open/Closed Principle
L: Liskov Substitution Principle
I: Interface Segregation Principle
D: Dependency Inversion Principle


Violation of SRP
Consider a component that displays a list of products and allows the user to filter them by category. Here’s an example of what the code might look like:

import { Component, OnInit } from '@angular/core';
import { ProductService } from './product.service';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent implements OnInit {

  products: any[];
  categories: string[];
  selectedCategory: string;

  constructor(private productService: ProductService) { }

  ngOnInit() {
    this.products = this.productService.getProducts();
    this.categories = this.productService.getCategories();
  }

  filterByCategory(category: string) {
    this.selectedCategory = category;
    this.products = this.productService.getProductsByCategory(category);
  }
}


Fixing the Violation
First, we’ll create a new component that displays the list of products:

import { Component, OnInit } from '@angular/core';
import { ProductService } from './product.service';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent implements OnInit {

  products: any[];

  constructor(private productService: ProductService) { }

  ngOnInit() {
    this.products = this.productService.getProducts();
  }

}

This component is now responsible only for displaying the list of products. We’ve removed the category filtering logic to a new component.

Next, we’ll create a new component that handles the category filtering:


import { Component, OnInit } from '@angular/core';
import { ProductService } from './product.service';

@Component({
  selector: 'app-product-filter',
  templateUrl: './product-filter.component.html',
  styleUrls: ['./product-filter.component.css']
})
export class ProductFilterComponent implements OnInit {

  categories: string[];
  selectedCategory: string;

  constructor(private productService: ProductService) { }

  ngOnInit() {
    this.categories = this.productService.getCategories();
  }

  filterByCategory(category: string) {
    this.selectedCategory = category;
    this.productService.getProductsByCategory(category);
  }

Violating the Open/Closed Principle
Consider, we have a component that displays a list of users. The users are retrieved and displayed in a table. The table has three columns: name, email, and phone number. We have created a UserListComponent to display this list. Initially, the component looks like this:

import { Component, OnInit } from '@angular/core';
import { UserService } from '../services/user.service';

@Component({
  selector: 'app-user-list',
  templateUrl: './user-list.component.html',
  styleUrls: ['./user-list.component.css']
})
export class UserListComponent implements OnInit {
  users: any[];

  constructor(private userService: UserService) { }

  ngOnInit() {
    this.users = this.userService.getUsers();
  }
}
In the template file user-list.component.html, we have the following code:

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Email</th>
      <th>Phone</th>
    </tr>
  </thead>
  <tbody>
    <tr *ngFor="let user of users">
      <td>{{ user.name }}</td>
      <td>{{ user.email }}</td>
      <td>{{ user.phone }}</td>
    </tr>
  </tbody>
</table>
Everything works fine, and we can see the list of users displayed in a table with three columns.

Now, suppose we want to add a new feature to the application, which is to allow users to view additional details for each user. We want to display the user’s address, city, and state. We can simply modify the UserListComponent to add these columns to the table. Here’s how we can do it:

import { Component, OnInit } from '@angular/core';
import { UserService } from '../services/user.service';

@Component({
  selector: 'app-user-list',
  templateUrl: './user-list.component.html',
  styleUrls: ['./user-list.component.css']
})
export class UserListComponent implements OnInit {
  users: any[];

  constructor(private userService: UserService) { }

  ngOnInit() {
    this.users = this.userService.getUsers();
  }
}
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Email</th>
      <th>Phone</th>
      <th>Address</th>
      <th>City</th>
      <th>State</th>
    </tr>
  </thead>
  <tbody>
    <tr *ngFor="let user of users">
      <td>{{ user.name }}</td>
      <td>{{ user.email }}</td>
      <td>{{ user.phone }}</td>
      <td>{{ user.address }}</td>
      <td>{{ user.city }}</td>
      <td>{{ user.state }}</td>
    </tr>
  </tbody>
</table>


Fixing the violation
To fix the violation of the OCP, we need to separate the concerns of displaying the list of users and displaying the details of each user. We can create a new component called UserDetailComponent to display the additional details for each user. Here’s how we can do it:

import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-user-detail',
  template: `
    <div>
      <p><strong>Address:</strong> {{ user.address }}</p>
      <p><strong>City:</strong> {{ user.city }}</p>
      <p><strong>State:</strong> {{ user.state }}</p>
    </div>
  `
})
export class UserDetailComponent {
  @Input() user: any;
}
In the UserListComponent, we can now use the UserDetailComponent to display the additional details for each user. Here’s how we can do it:

import { Component, OnInit } from '@angular/core';
import { UserService } from '../services/user.service';

@Component({
  selector: 'app-user-list',
  templateUrl: './user-list.component.html',
  styleUrls: ['./user-list.component.css']
})
export class UserListComponent implements OnInit {
  users: any[];

  constructor(private userService: UserService) { }

  ngOnInit() {
    this.users = this.userService.getUsers();
  }
}
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Email</th>
      <th>Phone</th>
    </tr>
  </thead>
  <tbody>
    <tr *ngFor="let user of users">
      <td>{{ user.name }}</td>
      <td>{{ user.email }}</td>
      <td>{{ user.phone }}</td>
      <td>
        <button (click)="user.showDetails = !user.showDetails">
          {{ user.showDetails ? 'Hide Details' : 'Show Details' }}
        </button>
        <app-user-detail *ngIf="user.showDetails" [user]="user"></app-user-detail>
      </td>
    </tr>
  </tbody>
</table>

Violating LSP
class Animal {
  move() {
    console.log('I can move');
  }
}

class Bird extends Animal {
  fly() {
    console.log('I can fly');
  }
}

class Ostrich extends Animal {
  move() {
    console.log('I can move, but I cannot fly');
  }
}

function moveAnimal(animal: Animal) {
  animal.move();
}

const bird = new Bird();
moveAnimal(bird); // outputs "I can move"

const ostrich = new Ostrich();
moveAnimal(ostrich); // outputs "I can move, but I cannot fly"


To fix the violation, we can modify the Ostrich class to behave more like Animal:

class Ostrich extends Animal {
  move() {
    console.log('I can move, but I cannot fly');
  }

  fly() {
    throw new Error('I cannot fly');
  }
}


To demonstrate this, let’s consider an example where a component needs to retrieve data from a service. The service provides several methods, including ones that are not required by the component.

interface DataService {
  getItems(): Observable<Item[]>;
  addItem(item: Item): Observable<Item>;
  updateItem(item: Item): Observable<Item>;
  deleteItem(id: number): Observable<boolean>;
}


The component only needs to retrieve items from the service, so it should only depend on the getItems method. However, if the component is written as follows:

class MyComponent {
  items: Item[];
  
  constructor(private dataService: DataService) {}
  
  ngOnInit() {
    this.dataService.getItems().subscribe(items => this.items = items);
  }
}



To fix this, we can create a new interface that only includes the getItems method and have the service implement this interface. The component can then depend on this new interface instead of the original DataService interface.

interface ItemsService {
  getItems(): Observable<Item[]>;
}

class DataService implements ItemsService {
  getItems() { ... }
  addItem(item: Item) { ... }
  updateItem(item: Item) { ... }
  deleteItem(id: number) { ... }
}

class MyComponent {
  items: Item[];
  
  constructor(private itemsService: ItemsService) {}
  
  ngOnInit() {
    this.itemsService.getItems().subscribe(items => this.items = items);
  }
}


Violation of DIP
Let’s consider an example where we have a CustomerService that depends on a CustomerRepository to fetch customer data. The implementation of CustomerService looks like this:

import { CustomerRepository } from './customer.repository';

@Injectable()
export class CustomerService {
  constructor(private customerRepository: CustomerRepository) {}

  getCustomers(): Observable<Customer[]> {
    return this.customerRepository.getCustomers();
  }
}


The implementation of CustomerRepository looks like this:

@Injectable()
export class CustomerRepository {
  private customers: Customer[] = [
    { id: 1, name: 'John' },
    { id: 2, name: 'Jane' },
    { id: 3, name: 'Jack' }
  ];

  getCustomers(): Observable<Customer[]> {
    return of(this.customers);
  }
}



The implementation of CustomerService looks like this:

import { ICustomerRepository } from './customer.repository';

@Injectable()
export class CustomerService {
  constructor(private customerRepository: ICustomerRepository) {}

  getCustomers(): Observable<Customer[]> {
    return this.customerRepository.getCustomers();
  }
}
The implementation of ICustomerRepository looks like this:

export interface ICustomerRepository {
  getCustomers(): Observable<Customer[]>;
}
The implementation of CustomerRepository now implements the ICustomerRepository interface.

@Injectable()
export class CustomerRepository implements ICustomerRepository {
  private customers: Customer[] = [
    { id: 1, name: 'John' },
    { id: 2, name: 'Jane' },
    { id: 3, name: 'Jack' }
  ];

  getCustomers(): Observable<Customer[]> {
    return of(this.customers);
  }
}






