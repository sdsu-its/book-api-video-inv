# Categories

Items are assigned to various categories to make organizing them inside the inventory manager easier. Categories can be assigned icons which are used throughout the application to provide a quick overview of what is included in a given transaction. Each item can be assigned AT MOST one category.

{% method %}
## GET `/category`

If you would like to retrieve information for a specific category, you will need to know the Category's ID, which is a unique integer identifier. You can also omit the category identifier in the request to include all categories in the response.

{% sample lang="js" %}
Retrieving information on a single category, for example Category ID: 100
```js
var categoryID = 100;

$.ajax({
  type: "GET",
  url: API_ROOT + '/category?id=' + categoryID,
  success: success,
  headers: {"session": SESSION_TOKEN}
});
```

Retrieving information on for all categories.
```js
$.ajax({
  type: "GET",
  url: API_ROOT + '/category',
  success: success,
  headers: {"session": SESSION_TOKEN}
});
```

{% common %}
Regardless of if you request one or multiple categories, the result will be a JSON array. 

```json
[
  {
    "id": 100,
    "name": "STO"
  }
]
```


If you specified a category ID, but a category with that ID does not exist, a **404 - Not Found** will be returned, along with the following status message.

```json
{
  "status": "Error",
  "message": "No Category with that ID was found."
}
```
{% endmethod %}

{% method %}
## POST `/category`

To create a NEW category, the user must be a supervisor, which is determined via the Session Token that is passed.
The category name is NOT checked for uniqueness, and multiple requests will result in multiple categories with the same attributes being created.

{% sample lang="js" %}
We will create a new category with the name of "Example".
```js
var payload = {};
payload.name = "Example";

$.ajax({
  type: "POST",
  url: API_ROOT + '/category',
  data: payload,
  dataType: 'json',
  success: success,
  headers: {"session": SESSION_TOKEN}
});
```

{% common %}
If the user is allowed to create categories, and the category was created a status of **201 - Created** is returned, along with the newly created Category.

```json
{
  "id": 108,
  "name": "Example"
}
```

If the user is not permitted to create new categories, a status of **403 - Forbidden** is returned, along with a JSON status message.
{% endmethod %}

{% method %}
## PUT `/category`

The PUT verb is used to update a category. The category must exist before it is updated, else an error message is returned. The Category must be identified via its ID when it is updated, since all other fields are mutable.

{% sample lang="js" %}
We will update category 100, with the name of "foo".

```js
var payload = {};
payload.id = 100;
payload.name = "foo";

$.ajax({
type: "PUT",
url: API_ROOT + '/category',
data: payload,
dataType: 'json',
success: success,
headers: {"session": SESSION_TOKEN}
});
```

{% common %}
If the user is allowed to create categories, and the category was updated successfully a status of **200 - OK** is returned, along with a status message.

```json
{
  "message": "Category Updated"
}
```

If no category with the specified ID does not exist, a status of **404 - Not Found** is returned, along with a JSON status message.

```json
{
  "status": "Error",
  "message": "Category Does not Exist"
}
```

If the user is not permitted to create new categories, a status of **403 - Forbidden** is returned, along with a JSON status message.
{% endmethod %}

{% method %}
## DELETE `/category`

The DELETE verb is used to delete a category. Similar to the create and update endpoints, the user must be an administrator to perform this action. Before a delete can be executed, all items need to be removed from the category. This is not done automatically, can if this is not the case, the request fails.

Only the category ID needs to be supplied in the request payload.

{% sample lang="js" %}
We will delete category 100.

```js
var payload = {};
payload.id = 100;

$.ajax({
type: "DELETE",
url: API_ROOT + '/category',
data: payload,
dataType: 'json',
success: success,
headers: {"session": SESSION_TOKEN}
});
```

{% common %}
If the user is allowed to delete categories, and the category was updated deleted a status of **200 - OK** is returned, along with a status message.

```json
{
  "message": "Category Deleted"
}
```

If the category still has items assigned to it, a status of **400 - Bad Request** is returned, along with a JSON status message.

```json
{
  "status": "Error",
  "message": "Category has items. Cannot Delete."
}
```

If no category with the specified ID does not exist, a status of **404 - Not Found** is returned, along with a JSON status message.

```json
{
"status": "Error",
"message": "Category Does not Exist"
}
```

If the user is not permitted to create new categories, a status of **403 - Forbidden** is returned, along with a JSON status message.
{% endmethod %}

{% method %}
## GET `/category/icon/:id`

Category Icons are handled through the Category endpoint, but with a somewhat different URL. This request does not require a session header, which allows it to be used in an `<img>` tag. However, an error code (404) is returned if an icon has no icon defined. 

You may want to add an `onerror` listener to the img tags to deal with this.  For example, for Category ID: 123...
``` html
<img src="api/category/icon/123" class="categoryIcon" onerror="this.style.visibility ='hidden'">
```

Using `visibility: hidden` keeps the spacing as if an icon were present, but hides the broken image icon that many browsers display if an error occurs while loading an image.

The image is served directly from the endpoint, with the proper headers and encoding. Images will always be PNGs, and will be approximately 150px.

{% endmethod %}

{% method %}
## POST `/category/updateICO/:id`

Updating a category icon is handled separately from updating a category's name, because a multi-part form message is required. The name of the file is important, since the extension is used to determine the format of the file. The image will be converted to PNG with a white background once uploaded, and will be resized to a pre-defined max size (150px be default).

A session token is also required with this request.

{% sample lang="js" %}
Assuming that `icon` is a file that was collected via an `<input type="file">` tag, and that `categoryID` is the category we would like to update.

```js

    var formData = new FormData();
    formData.append('icon', icon, icon.name);

    var xmlHttp = new XMLHttpRequest();

    xmlHttp.onreadystatechange = function () {
       console.log(xmlHttp.responseText);
    };

    xmlHttp.open('POST', "api/category/updateICO/" + categoryID);
    xmlHttp.setRequestHeader("session", SESSION);
    xmlHttp.send(formData);

```

{% common %}
If the user is allowed to update categories, and the category icon was updated was created a status of **202 - Accepted** is returned, along with a status message.

```json
{
  "message": "Category 123 icon updated"
}
```

Should an error occur in processing the file (which is unlikely, but still possible), a status of **500 - Internal Server Error** will be returned, with a message on what went wrong.

If the user is not permitted to update categories, a status of **403 - Forbidden** is returned, along with a JSON status message.
{% endmethod %}


