# Task 10: Connecting the Frontend with the Backend

## Description

Now that we have implemented the frontend and the backend of the final project, its time to connect these two layers.

## Walkthrough

### Step 1: Using fetch to POST the new Item

> #### Useful Resources for this step
>
> - [Using Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)

In this step, we'll use the Fetch API to consume the save item endpoint.

1. In `js/ItemsController.js`, implement a new function called `save` that will POST the new item's data using the `fetch` function:

   ```javascript
       save({name, description, imageUrl}){
               const data = { name,  description, imageUrl };

               fetch('http://localhost:8080/item', {
               method: 'POST', // or 'PUT'
               headers: {
                   'Content-Type': 'application/json',
               },
               body: JSON.stringify(data),
               })
               .then(response => response.json())
               .then(data => {
               console.log('Success:', data);
               })
               .catch((error) => {
               console.error('Error:', error);
               });
           }
   ```

2. Modify the `save` method, the `getItems` method, the `findItemById` method, the `update` method, and the `delete` method of the `ItemsController.java` to avoid the [CORS mechanism](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

   ```java
       @CrossOrigin
       @PostMapping
       public Item save( @RequestBody ItemDto itemDto )
       {
           return itemService.save( new Item( itemDto ) );
       }
   ```

3. Add a call to the `save` function inside the scope of `addItem` function (you could call it after the items are stored on the `localStorage`).

4. In `items.js`, create a `loadItemsFromDatabase()` function. Use `fetch` to make a call to your `GET` Route. ie: `fetch('http://localhost:8080/item/all')`. Once you receive your response from your `GET` request, use a `for loop` to populate your `items` Array with the data received. Finally, call `loadCardsListFromItemsController()` from within the `loadItemsFromDatabase()` function. ie: 
   ```javascript
    function loadItemsFromDatabase() {
    fetch('http://localhost:8080/item/all')
    .then(response => response.json())
    .then(data => {
        for(let i = 0; i < data.length; i++) {
            itemsController.items.push(data[i]);                
        }   
    })
    .then(() => {
        loadCardsListFromItemsController();
    })
    .catch((error) => {
        console.log('Error: ', error);
    })   
    ```
Call your `loadItemsFromDatabase()` at the bottom of `items.js`.

5. Now that you are getting your `items` from the Database, you don't need to use `localStorage` anymore. Inside of `ItemsController.js`, remove everything from within your constructor so that you have only the following line of code:
```javascript
constructor() {
        this.items = [];
    }
```

> #### Test Your Code!
>
> Now is a good chance to test your code, start your Server API and verify if the data is stored correctly on the MySQL database.
>
> 1. Open `item-form.html` in the browser and create a new item using the form.
>
> **Expected Result**
> You should see the success message on the console log.
>
> 2. Open `items.js` in your browser.
>
> **Expected Result**
> You should see the products/posts from your database displayed on your webpage.

### Step 2: Consuming REST API

> #### Useful Resources for this step
>
> - [How to use fetch api for crud operations](https://dev.to/duhbhavesh/how-to-use-fetch-api-for-crud-operations-57a0)

Now that we have made the implementation of the `save` function to consume the `POST /item` endpoint and the `loadItemsFromDatabase` function to consume the `GET /item/all` endpoint we'll implement the missing functions:

- delete
- update
- findByItemId

1. Add an `update` button and a `delete` button to the product/post card inside of your `addItemCard` function in `items.js`. Add the class `btn-delete` to your newly created `delete` button.
2. Add an `id` attribute to your product/post card inside of your `addItemCard` function in `items.js`.
3. Add an event listener to your delete button listening for a `click` event.
4. Save the `parentElement` of your delete button to a variable called `item`.
5. Call your `delete` method and pass in `item.id` as the argument.
6. Use `location.reload()` to refresh your page so that your items are reloaded.
```javascript
    function addItemCard(item){
    const itemHTML = '<div id="'+item.id +'" class="card" style="width: 20rem;">\n' +
        '    <img src="'+item.imageUrl +'" width="300" height="250"  alt="product image">\n' +
        '    <div class="card-body">\n' +
        '        <h5 class="card-title">'+item.name+'</h5>\n' +
        '        <p class="card-text">'+item.description+'</p>\n' +
        '        <a href="#" class="btn btn-primary">Add</a>\n' +
        '        <a href="./item_update_form" class="btn btn-primary">Update</a>\n' +
        '        <a href="#" class="btn btn-danger btn-delete">Delete</a>\n' +
        '    </div>\n' +
        '</div>\n' +
        '<br/>';
    const itemsContainer = document.getElementById("list-items");
    itemsContainer.innerHTML += itemHTML;

    let deleteButton = document.getElementsByClassName("btn-delete");
    for(let i = 0; i < deleteButton.length; i++) {
        let deleteBtn = deleteButton[i];
        deleteBtn.addEventListener("click", () => {
            let item = deleteBtn.parentElement.parentElement;
            itemsController.delete(item.id);
            location.reload();
        })            
    }
}
``` 
7. In `js/ItemsController.js`, implement the `delete` function so it will `DELETE` the item's data using the `fetch` function:
```javascript
    delete(itemId){
        fetch(`http://localhost:8080/item/${itemId}`, {
            method: 'DELETE',
            headers: {
                'Content-type': 'application/json'
            }
        })
        .then(response => console.log("Success:", response))
        .catch((error) => {
            console.error("Error:", error);
        })
    }
```
 
> #### Test Your Code!
>
> Now is a good time to test your code, start your Server API.
>
> 1. Open `items.html` in your browser and click on the `delete` button.
>
> **Expected Result**
> You should see the item removed from your page and from your database.
>

8. Now we will implement the `update` function. 
9. Create a new html file called `update_form.html` in your root directory and a new javaScript file called `update-form.js` in your `js` folder. Make sure you use `script` tags to link your `ItemsController.js` and `update-form.js` files in your `update_form.html` file in that order.
10. Add the required code to your `update_form.html` file. (Should be similar to the code in `item_form.html`). You will need a `form` element that accepts inputs for the values you wish to update.
11. Inside your `item.js` file, you will need to decide how you want to implement the `update` feature. You could choose to add an `href` attribute to all your `updateButton`'s and redirect the user to your `update_form.html` page when any of the `updateButton`'s are clicked or you could use an `eventListener` and listen for which specific `updateButton` is clicked so that you update the item who's `updateButton` was actually clicked.
12. Inside your `update-form.js` file, you will need to do almost the same things that you did in your `item-form.js` file however you will need to call your `itemsController.update()` method passing in the values from your `update_form.html` file.
13. Inside your `ItemsController.js` file you will need to implement your `update()` method. This method should take in any values you would like to have updated and use the `fetch` api to consume your `PUT item/id` endpoint on your server. (Your `update` method will be similar to you `save` method).
14. Test the `update` function(make sure you send an existing item id)
> #### Test Your Code!
>
> Now is a good time to test your code, start your Server API.
>
> 1. Open `items.html` in your browser and click on the `update` button.
>
> 2. Enter new values for your product/post.
>
> **Expected Result**
> You should see the product/post's values updated on your page and in your database.
>
15. Implement the `findItemById`
12. Change your code so the save button calls the `findItemById` function.
13. Populate the item's data into the corresponding form fields.
14. Test the `findItemById` function (make sure you send an existing item id)

## Results

Now you have all the methods to consume the REST API CRUD operations. Congratulations!

## Example

Stuck? Check out the provided example in the [example/](example/) folder
