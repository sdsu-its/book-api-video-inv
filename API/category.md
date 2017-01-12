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