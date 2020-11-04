# Submit an HTML form and get an SMS text on every form submission

Have an front end or HTML form? Let's save the responses and get an SMS notification on every submission. This typically would require you to host a database and endpoints, deploy them, and integrate them to your front end. Brev was designed to handle all of this for us, including the SMS!

## Step 1: Create an endpoint

From the CLI:

```bash
brev add endpoint FormPoster
```

From the [web app](https://app.brev.dev), just click the "+ Endpoint" button:

![image](static/addEp.png ":size=350")

## Step 2: Send data from your form to Brev server

We need to create an API endpoint to accept the form data. We should do this with a POST call. The form data will need to be JSON.

On Brev, we can handle a POST call by creating a function called `post`. Brev will call the approriate function based on the HTTP request type.

Replace your endpoint code with the following:

```python
import variables
import shared
from pydantic import BaseModel
import sms

# we'll come back to this later
def get():
  return {}

# this is what the JSON being submitted from the form should look like.
# our form will have 3 fields called name, email, and issue
class FormData(BaseModel):
  name: str
  email: str
  issue: str

# in the parenthesis below, we're passing a variable called data, that is of type FormData defined above
# Brev will automatically match the JSON, create the variable, and it's passed in to our function.
def post(data: FormData):
  # from the Brev sms package, imported above
  sms.send("4155555555", f"Issue {data.issue} was created!")
  return data.dict() # we can freely use data as a variable now.
```

# Step 3: Store the form data in a database

We are going to use Brev's storage_context as the database. It's a simple key-value store. This will let it operate like a dictionary. The key to each task will need to be unique, so we can use a UUID.

```python
import variables
import shared
from global_storage import storage_context # the Brev database
import uuid # we'll use this library to generate unique IDs

from pydantic import BaseModel
import sms

# we'll come back to this later
def get():
  return {}



class FormData(BaseModel):
  name: str
  email: str
  issue: str


# pass in the database to store items.
# By naming it "issues", we're getting the database called "issues", if it doesn't exist, Brev will create it for us.
def post(data: FormData, issue_storage=storage_context("issues")):
  unique_uuid = str(uuid.uuid4()) # generate a unique UUID as the key
  issue_storage[unique_uuid] = data.dict() # store the issue
  sms.send("4155555555", f"Issue {data.issue} was created!")
  return data.dict()
```

# Step 4: Make an API endpoint to get all issues in the database

```python
import variables
import shared
from global_storage import storage_context
import uuid

from pydantic import BaseModel
import sms

# pass in the same database as the post call
def get(issue_storage=storage_context("issues")):
  return issue_storage.items() # return the items!



class FormData(BaseModel):
  name: str
  email: str
  issue: str




def post(data: FormData, issue_storage=storage_context("issues")):
  unique_uuid = str(uuid.uuid4())
  issue_storage[unique_uuid] = data.dict()
  sms.send("4155555555", f"Issue {data.issue} was created!")
  return data.dict()
```

# Step 5: Make a basic HTML form to submit the data

```HTML
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1, shrink-to-fit=no"
    />
    <meta name="description" content="" />
    <meta
      name="author"
      content="Mark Otto, Jacob Thornton, and Bootstrap contributors"
    />
    <meta name="generator" content="Jekyll v4.1.1" />
    <title>Brev Form Submission</title>

    <link
      rel="canonical"
      href="https://getbootstrap.com/docs/4.5/examples/sign-in/"
    />

    <!-- Bootstrap core CSS -->
    <link
      rel="stylesheet"
      href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css"
    />

    <style>
      .bd-placeholder-img {
        font-size: 1.125rem;
        text-anchor: middle;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
      }

      @media (min-width: 768px) {
        .bd-placeholder-img-lg {
          font-size: 3.5rem;
        }
      }
    </style>
    <!-- Custom styles for this template -->
    <link href="signin.css" rel="stylesheet" />
  </head>
  <body class="text-center">
    <form class="form-signin">
      <h1 class="h3 mb-3 font-weight-normal">Create a todo</h1>
      <label for="inputEmail" class="sr-only">Email address</label>
      <input
        type="email"
        id="inputEmail"
        class="form-control"
        placeholder="Email address"
        required
        autofocus
      />
      <label for="inputName" class="sr-only">name</label>
      <input
        type="text"
        id="inputName"
        class="form-control"
        placeholder="name"
      />
      <label for="inputIssue" class="sr-only">todo</label>
      <input
        type="text"
        id="inputIssue"
        class="form-control"
        placeholder="todo"
      />
      <button
        class="btn btn-lg btn-primary btn-block"
        type="button"
        id="submitBtn"
      >
        Submit To-Do
      </button>
      <p class="mt-5 mb-3 text-muted">baked from scratch with ❤️</p>
    </form>
  </body>

  <script>
    document.getElementById("submitBtn").onclick = function () {
      // Create JSON object of to do item
      // Ensure we match the pydantic class defined in our Brev code
      var newToDo = {
        name: document.getElementById("inputName").value,
        email: document.getElementById("inputEmail").value,
        issue: document.getElementById("inputIssue").value,
      };
      // POST the data to our brev url.
      //NOTE: MAKE SURE to change the brev_url below
      fetch(brev_url, { method: "POST", body: JSON.stringify(newToDo) });
    };
  </script>
</html>

```

Simply visit that url, or create a fetch using the GET method to view all the issues!

# Optional Step 6: Delete an issue

To perform a delete, we should use the HTTP DELETE method. To handle the DELETE request, we just need to create a function called delete.

```python
import variables
import shared
from global_storage import storage_context
import uuid

from pydantic import BaseModel
import sms


def get(issue_storage=storage_context("issues")):
  return issue_storage.items()



class FormData(BaseModel):
  name: str
  email: str
  issue: str




def post(data: FormData, issue_storage=storage_context("issues")):
  unique_uuid = str(uuid.uuid4())
  issue_storage[unique_uuid] = data.dict()
  sms.send("4155555555", f"Issue {data.issue} was created!")
  return data.dict()


# the delete call will take the id as a query parameter. This is the id to delete.
# we also need to pass in a reference to the database we plan to delete from
def delete(id, issue_storage=storage_context("issues")):
  deleted_item = issue_storage.pop(id) # deletes the item, or pops it out of the database

  return {"deleted": deleted_item} #return the deleted item for confirmation

```

# That's all!

Hope it was helpful. Join our slack community and let us know if there's anything you're stuck on, or an exmaple that would be helpful
