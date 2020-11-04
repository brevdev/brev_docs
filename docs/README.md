## Getting Started Guide

Brev lets you build a production server in seconds without worrying about frameworks, hosting, deploying, or scaling.

**Note: this is our quick and recommended way to get started. It'll take ~10 min to complete.
<a href="/#/full" target="_blank">Head here if you're looking for the full documentation.</a>**

**Highlighted Features**:

- **Production ready by default**: Scale without extra configuration or additional knowledge
- **Deployless**: Deployments just happen instantly when you save.
- **Ergonomic request/response validation**: Use python3 type hints to define the shape of your request args and body.
- **Openapi by default**: Openapi specification is automatically generated for you as you write and annotate your endpoints
- **Secrets management**: Store your applications secrets securely without extra effort
- **Package management**: Use the power of python's community with 3rd party packages found on pypi
- **Shortstore:** A super simple nosql database to prototype your applications -- backed by Dynamodb.

**Coming Soon**:

Our roadmap is constantly evolving with feedback from our users. Here are some of the things we are thinking about.

- **Eject:** Eject your project at any point to host it yourself and get under the hood. Access all the configuration details and source code without worrying about them at the start of your project.
- **Project Environments:** Create multiple isolated environments to develop on ex. (staging, prod, dev) and promote between.
- **Cron Jobs** Create and manage cron jobs without any extra setup.
- **Application monitoring, alerting:** Get crash alerts and performance monitoring out of the box or hook up (Sentry, APM, etc).
- **Robust Logging and Tracing** Search and query for relevant logs to quickly debug your application and trace events.

Use this quickstart guide to jump into Brev. If you have any lingering questions, [please e-mail us](nader+docs@brev.dev)

**Project Status**

[✓] Alpha: We are testing Brev with a small set of users.

[✓] Private Alpha: Join our waitlist and we will get you access as soon as we can. We hope you build something great! The platform still has kinks please help us iron them out.

[ ] Public Beta: Anyone can create an account. API becomes stable.

[ ] Public launch: Stable for enterprise use cases.

### What we will accomplish

We will go over the core features of Brev by building a sign up form that texts you everytime someone signs up.

**7 steps, eta: 10 minutes**

### Setup

- Requires a Brev account.

#### Webapp Setup

Login to https://app.brev.dev

#### CLI Setup

Install the CLI with pip:

```zsh
$ pip install https://github.com/brevdev/brev_cli/raw/master/dist/brev-cli-0.2.0.tar.gz
```

> **Note** ps: If you get direct dependency error, please upgrade pip with `pip install --upgrade pip`

Go to any directory and run

```zsh
brev init
```

to create a project. The CLI will use the directory name for the project. If you want to pull an already created project, simply add the name

```zsh
brev init <project_name>
```

### Create a basic endpoint

**Step 1 of 7**

With Brev, your code is deployed by default. Simply create an endpoint, and it's ready to be consumed for your use case at any\* scale.

> \*Checkout our current [scale limitations](https://docs.brev.dev/#/full?id=limitations) and how you can extend them.

From the webapp: click "+" to create an endpoint. Give it a name, and click save!
From the CLI: type `brev add endpoint <your EP name>`

Your endpoint is saved and hosted!

> **Webapp**: At the top of the code editor, you'll see the hosted URL. It's ready to go and be used in any app you're building. Go to "home" and you'll see all your endpoints, including this one.

> **CLI**: You'll need to be in the active project directory. Creating a new endpoint will add a python file for the endpoint in that directory. `brev list` will get you the URLs for every endpoint. If something didn't work, make sure to login and initialize! `brev login` followed by `brev init`.

Now let's make the endpoint do something.

### GET request with a query args

To accept a parameter to your endpoint as a query argument, simply add the argument to the function and use it like a variable!

> **Note**: Query arguments are key value pairs found at the end of urls. ex (`?query_arg=123&other_query_arg=456`)

```python
def get(greeting):
  return {"response": f"{greeting} world!"}
```

Now let's hit the endpoint!

> In the webapp: below the code editor, you can use the endpoint runner. Select GET and query params. Type greeting into the table, and hola as the value. Brev might've predicted the query args and type it in for you.

> In the CLI: `brev run endpointName GET --args greeting=hola`. Note if you set up shell autocomplete, you can hit tab and the CLI will suggest the available endpoints and HTTP request types! Also, you can type -a instead of --args.
> Note: you can also just open a new tab and manually type in the query argument.

The response should be as follows:

```json
{"response": "hola world"} // GET /endpointurl?greeting=hola

{"response": "marhaba world"} // GET /endpointurl?greeting=marhaba
```

Read more about how this works
<a href="/#/full?id=query-arguments" target="_blank">here</a>

Your endpoint can now accept data! Now let's do a POST.

### POST call with JSON body

The function name from the previous example was `get`. You can use any valid HTTP request type, and Brev will pattern match the appropriate function based on the incoming HTTP request. Read more
<a href="/#/full?id=handling-http-methods" target="_blank">here</a>
. So to handle a POST call, we just add a function called `post`.

```python
def post():
    return {}
```

For query arguments in the previous example, we just used a variable, but JSON bodies can be more complicated, so we can create a class representing the incoming data and pass it in to our `post` function.

```python
from pydantic import BaseModel

# type definition of incoming JSON body
class User(BaseModel):
    name: str # yay types!
    phone: str # yay types!

# pass it in to function
def post(user: User):
    return {
        "status": f"{user.name} says hi!"
    }
```

Now let's test it with the following JSON body:

```json
{
  "name": "Brev",
  "phone": "4158180207"
}
```

> CLI: create a new json file `touch input.json` and edit it with the contents. `brev run MyEndpoint POST --body /pathTo/input.json`. With shell autocomplete enabled, you can autocomplete the endpoint name, the HTTP request type, and the available json files.

> Webapp: select POST instead of GET from the drop down, select JSON for the payload type.

```json
{
  "status": "Brev says hi!"
}
```

We're defining a [Pydantic](https://pydantic-docs.helpmanual.io) model to define what our data should look like. Read more
<a href="/#/full?id=request-body" target="_blank">here</a>

Next we'll notify you everytime a new user gets created!

### Get an SMS text every signup!

Brev comes preconfigured with a lot of out-of-the-box functionality such as SMS. To send texts, just `import sms` and use `sms.send`. Read more
<a href="/#/full?id=Brev-tools" target="_blank">here</a>

```python
from pydantic import BaseModel
import sms

class User(BaseModel):
    name: str # yay types!
    phone: str # yay types!

def post(user: User):
    sms.send("4158180207", f"{user.name} created an account!")
    return {
        "status": f"{user.name} says hi!"
    }
```

Hit the endpoint again and you should get a text!

In just a few minutes you were able to get live, deployed, endpoints and SMS functionality! No configuration needed :tada:

### Storage - Persist data

The next step to your MVP is persisting data between endpoint calls. We can do this with the Shortstorage object.
<a href="/#/full?id=shortstorage" target="_blank">Read more about Shortstorage here.</a>

```python
    from pydantic import BaseModel
    import sms
>   from global_storage import storage_context

    class User(BaseModel):
        name: str # yay types!
        phone: str # yay types!

>   def post(user: User, user_store = storage_context("users")):
        user_store[user.phone] = user.dict()

        sms.send("4158180207", f"{user.name} created an account!")
        return {
            "status": f"{user.name} says hi!"
        }
```

Every time you hit the endpoint, you'll store the users! If you're in the webapp, click on "storage" from the navbar to view the state object.

We can make a simple API to build a dashboard with your users. Create a new endpoint with the following:

```python
from global_storage import storage_context

def get(user_store = storage_context("users")):
    all_users = user_store['users'].items()
    return {"users": all_users}

```

> Note: Shortstorage is a great way to get up and running, but it won't scale as elegantly as your endpoints. We're consistently improving it, but if you have an intense amount of data, it might be best to hook up your own database.

### Shared Code

Every Brev project has a Shared Code file, accessible by every endpoint in the project. This is a good place to isolate the storage code.

<a href="/#/full?id=shared-code" target="_blank">Read more here.</a>

> CLI: every project folder will have a shared.py for shared code

> Webapp: every endpoint editor page has a tab for shared code

Let's move the storage logic to a shared code function

```python
# shared code

def store_user(user, store)
    store[user.phone] = user.dict()

```

Now update the endpoint to use this function. Don't forget the import!

```python
    from pydantic import BaseModel
    from global_storage import storage_context
    import sms
>   import shared

    class User(BaseModel):
        name: str # yay types!
        phone: str # yay types!

    def post(user: User, user_store = storage_context("users")):
>       shared.store_user(user, user_store)
        sms.send("4158180207", f"{user.name} created an account!")
        return {
            "status": f"{user.name} says hi!"
        }
```

Voila! Now we can modify our storage logic without disrupting your endpoint :)

### Variables

In the examples above, I've been using my phone number in the code.
This is a bad practice, so we can use Variables!
<a href="/#/full?id=variables" target="_blank">Read more here.</a>
(P.S. that is my real number, shoot me a text and let me know how it's going!)

> CLI: run `brev add variable PHONENUMBER` and it will prompt you for the value.

> Web app: Click on `Variables` on the nav bar on the left. Add the new variable to the table.

Now we just need to import variables in our code and we can use the values!

```python
    from pydantic import BaseModel
    from global_storage import storage_context
    import sms
    import shared
>   import variables

    class User(BaseModel):
        name: str # yay types!
        phone: str # yay types!

    def post(user: User, user_store = storage_context("users")):
        shared.store_user(user, user_store)
>       sms.send(variables.PHONENUMBER, f"{user.name} created an account!")
        return {
            "status": f"{user.name} says hi!"
        }
```

### Final Thoughts

Hopefully this was a useful introduction to Brev!
<a href="/#/full" target="_blank">The full docs are here.</a>

Do you have any questions? Feature requests? Something missing from the docs? Wanna chat?

Talk to us!

Nader:
cell: 4158180207
email: nader+docs@brev.dev

### Full Documentation

Click to go to the [full documentation](/full?id=brev)

### Full App with User Auth

If you're trying to create a full app with user authentication, [use this guide and starter code!](/auth?id=build-a-full-app-with-user-authentication)
