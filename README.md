# Build a To-Do application Using Django and React | DigitalOcean

​

[digitalocean.com](https://www.digitalocean.com/community/tutorials/build-a-to-do-application-using-django-and-react#setting-up-the-frontend)

24-31 minutes

------

## Introduction

In this tutorial, we build a Todo application using Django and React.

**React** is a JS framework that is great for developing SPAs (**s**ingle **p**age **a**pplications) and it has [solid documentation](https://reactjs.org/docs/getting-started.html) and a vibrant ecosystem around it.

**Django** is a Python web framework that simplifies  common practices in web development. Django has been around for a while, meaning most gotcha’s and problems have been solved, and there’s a set  of stable libraries supporting common development needs.

For this application, React serves as the **front-end** or client side framework, handling UI and getting and setting data via requests to the Django **back-end**, which is an API built using the Django REST framework (DRF).

At the end of this tutorial, we will have the final application that looks like this:

![img](https://scotch-res.cloudinary.com/image/upload/v1542486383/kbrt3naby6pzcvhntpgr.gif)

The source code for this tutorial is available [here](https://github.com/do-community/django-todo-react) on GitHub.

## Prerequisites

To follow along with this tutorial, you will need to:

1. [Install and set up a local programming environment for Python 3](https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3)
2. [Install Node.js and Create a Local Development Environment](https://www.digitalocean.com/community/tutorial_series/how-to-install-node-js-and-create-a-local-development-environment)

## Setting up the Backend

In this section, we will set up the backend and create all the  folders that we need to get things up and running, so launch a new  instance of a terminal and create the project’s directory by running  this command:

Next, we will navigate into the directory:

Now we will install Pipenv using pip and activate a new virtual environment:

> Note: You should skip the first command if you already have Pipenv installed.

Let’s install Django using Pipenv then create a new project called backend:

Next, we will navigate into the newly created backend folder and  start a new application called todo. We will also run migrations and  start up the server:

At this point, if all the commands were entered correctly, we should  see an instance of a Django application running on this address — [http://localhost:8000](http://localhost:8000/)

![img](https://scotch-res.cloudinary.com/image/upload/v1542486456/ia8jlkozut4uxwatnqwp.png)

### Registering the Todo application

We are done with the basic setup for the backend, let’s start with the more advanced things like registering the `todo` application as an installed app so that Django can recognise it. Open the `backend/settings.py` file and update the `INSTALLED_APPS` section as so:

### Defining the Todo model

Let’s create a model to define how the Todo items should be stored in the database, open the `todo/models.py` file and update it with this snippet:

The code snippet above describes three properties on the Todo model:

- Title
- Description
- Completed

The completed property is the status of a task; a task will either be completed or not completed at any time. Because we have created a Todo  model, we need to create a migration file and apply the changes to the  database, so let’s run these commands:

We can test to see that CRUD operations work on the Todo model we  created using the admin interface that Django provides out of the box,  but first, we will do a little configuration.

Open the `todo/admin.py` file and update it accordingly:

We will create a superuser account to access the admin interface with this command:

> You will be prompted to enter a username, email and password for the  superuser. Be sure to enter details that you can remember because you  will need them to log in to the admin dashboard shortly.

Let’s start the server once more and log in on the address —   [http://localhost:8000/admin:](http://localhost:8000/admin)

![img](https://scotch-res.cloudinary.com/image/upload/v1542486514/bqvrdk3rrtxlueb7isig.png)

We can create, edit and delete Todo items using this interface. Let’s go ahead and create some:

![img](https://scotch-res.cloudinary.com/image/upload/v1542486530/mqxanwahs06racl3b6xz.png)

Awesome work so far, be proud of what you’ve done! In the next  section, we will see how we can create the API using the Django REST  framework.

## Setting up the APIs

Now, we will quit the server (CONTROL-C) then install the `djangorestframework` and `django-cors-headers` using Pipenv:

We need to add `rest_framework` and `corsheaders` to the list of installed applications, so open the `backend/settings.py` file and update the `INSTALLED_APPS` and `MIDDLEWARE` sections accordingly:

Add this code snippet to the bottom of the `backend/settings.py` file:

Django-cors-headers is a python library that will help in preventing  the errors that we would normally get due to CORS. rules. In the `CORS_ORIGIN_WHITELIST` snippet, we whitelisted `localhost:3000` because we want the frontend (which will be served on that port) of the application to interact with the API.

### Creating serializers for the Todo model

We need serializers to convert model instances to JSON so that the  frontend can work with the received data easily. We will create a `todo/serializers.py` file:

Open the `serializers.py` file and update it with the following code.

In the code snippet above, we specified the model to work with and the fields we want to be converted to JSON.

### Creating the View

We will create a `TodoView` class in the `todo/views.py` file, so update it with the following code:

The `viewsets` base class provides the implementation for  CRUD operations by default, what we had to do was specify the serializer class and the query set.

Head over to the `backend/urls.py` file and completely replace it with the code below. This code specifies the URL path for the API:

This is the final step that completes the building of the API, we can now perform CRUD operations on the Todo model. The router class allows  us to make the following queries:

- `/todos/`  - This returns a list of all the Todo items (Create and Read operations can be done here).
- `/todos/id`  - this returns a single Todo item using the id primary key (Update and Delete operations can be done here).

Let’s restart the server and visit this address —  <http://localhost:8000/api/todos>:

![img](https://scotch-res.cloudinary.com/image/upload/v1542486632/n9m3vnno99gawpa12xbi.png)

We can create a new todo item using the interface:

![img](https://scotch-res.cloudinary.com/image/upload/v1542486645/v0rvnmzbkrjrfqr1314d.png)

If the Todo item is created successfully, you will see a screen like this:

![img](https://scotch-res.cloudinary.com/image/upload/v1542486663/uhrtpofc3gas1ko2rqbh.png)

We can also perform DELETE and UPDATE operations on specific Todo items using their `id` primary keys. To do this, we will visit an address with this structure `/api/todos/id.` Let’s try with this address — <http://localhost:8000/api/todos/1>:

![img](https://scotch-res.cloudinary.com/image/upload/v1542486681/gxbmvfhplf1ga5indnkx.png)

That’s all for the backend of the application, now we can move on to fleshing out the frontend.

## Setting up the frontend

We have our backend running as it should, now we will create our  frontend and make it communicate with the backend over the interface  that we created.

Since we are building our frontend using React, we want to use the `create-react-app` CLI tool because it registers optimal settings and several benefits  such as Hot reloading and Service workers. We will install the `create-react-app`  CLI (command line interface) tool globally with this command:

Let’s navigate back into the parent working directory — `django-todo-react` — of our application and create a new React application called frontend:

It will probably take a while for all of the dependencies to be  installed, once it’s over, your terminal should look something like  this:

![img](https://scotch-res.cloudinary.com/image/upload/v1542487717/g9pqji3knpjcrxrsx6ue.png)

Run the following commands to navigate into the working directory and start the frontend server

> Note: If you don’t have Yarn installed, you can find installation instructions here.

We can now visit this address — [http://localhost:3000](http://localhost:3000/) — and we will see the default React screen:

![img](https://scotch-res.cloudinary.com/image/upload/v1542489344/hw2u89omngzpgttn1fj2.png)

We will pull in `bootstrap`  and `reactstrap` to spice the UI up a bit:

Let’s open the `src/index.css` file and replace the styles there with this one:

We will import Bootstrap’s stylesheet in `src/index.js` so that we can use Bootstrap’s classes:

Let’s replace the code in `src/App.js` with this one:

```jsx
  // frontend/src/App.js

    import React, { Component } from "react";
    const todoItems = [
      {
        id: 1,
        title: "Go to Market",
        description: "Buy ingredients to prepare dinner",
        completed: true
      },
      {
        id: 2,
        title: "Study",
        description: "Read Algebra and History textbook for upcoming test",
        completed: false
      },
      {
        id: 3,
        title: "Sally's books",
        description: "Go to library to rent sally's books",
        completed: true
      },
      {
        id: 4,
        title: "Article",
        description: "Write article on how to use django with react",
        completed: false
      }
    ];
    class App extends Component {
      constructor(props) {
        super(props);
        this.state = {
          viewCompleted: false,
          todoList: todoItems
        };
      }
      displayCompleted = status => {
        if (status) {
          return this.setState({ viewCompleted: true });
        }
        return this.setState({ viewCompleted: false });
      };
      renderTabList = () => {
        return (
          <div className="my-5 tab-list">
            <span
              onClick={() => this.displayCompleted(true)}
              className={this.state.viewCompleted ? "active" : ""}
            >
              complete
            </span>
            <span
              onClick={() => this.displayCompleted(false)}
              className={this.state.viewCompleted ? "" : "active"}
            >
              Incomplete
            </span>
          </div>
        );
      };
      renderItems = () => {
        const { viewCompleted } = this.state;
        const newItems = this.state.todoList.filter(
          item => item.completed == viewCompleted
        );
        return newItems.map(item => (
          <li
            key={item.id}
            className="list-group-item d-flex justify-content-between align-items-center"
          >
            <span
              className={`todo-title mr-2 ${
                this.state.viewCompleted ? "completed-todo" : ""
              }`}
              title={item.description}
            >
              {item.title}
            </span>
            <span>
              <button className="btn btn-secondary mr-2"> Edit </button>
              <button className="btn btn-danger">Delete </button>
            </span>
          </li>
        ));
      };
      render() {
        return (
          <main className="content">
            <h1 className="text-white text-uppercase text-center my-4">Todo app</h1>
            <div className="row ">
              <div className="col-md-6 col-sm-10 mx-auto p-0">
                <div className="card p-3">
                  <div className="">
                    <button className="btn btn-primary">Add task</button>
                  </div>
                  {this.renderTabList()}
                  <ul className="list-group list-group-flush">
                    {this.renderItems()}
                  </ul>
                </div>
              </div>
            </div>
          </main>
        );
      }
    }
    export default App;
```

Okay, that’s a lot of code ?, but there’s no need to be afraid now,  we haven’t started interacting with the backend API, so we included  default values to populate the Todo list. The `renderTabList()` function renders two spans which help control which set of items are displayed  i.e clicking on the completed tab shows completed tasks and the same for the incomplete tab.

If we visit the React frontend application now, it will look like this:

![img](https://scotch-res.cloudinary.com/image/upload/v1542489393/zs4pzlgpzvmmacvz9wzd.png)

To handle actions such as adding and editing tasks, we will use a modal, so let’s create a Modal component in a `components`  folder.

Create a `components` folder in the `src` directory:

Create a `Modal.js file` in the components folder:

Open the `Modal.js` file and populate it with the code snippet below:

```jsx
 // frontend/src/components/Modal.js

    import React, { Component } from "react";
    import {
      Button,
      Modal,
      ModalHeader,
      ModalBody,
      ModalFooter,
      Form,
      FormGroup,
      Input,
      Label
    } from "reactstrap";

    export default class CustomModal extends Component {
      constructor(props) {
        super(props);
        this.state = {
          activeItem: this.props.activeItem
        };
      }
      handleChange = e => {
        let { name, value } = e.target;
        if (e.target.type === "checkbox") {
          value = e.target.checked;
        }
        const activeItem = { ...this.state.activeItem, [name]: value };
        this.setState({ activeItem });
      };
      render() {
        const { toggle, onSave } = this.props;
        return (
          <Modal isOpen={true} toggle={toggle}>
            <ModalHeader toggle={toggle}> Todo Item </ModalHeader>
            <ModalBody>
              <Form>
                <FormGroup>
                  <Label for="title">Title</Label>
                  <Input
                    type="text"
                    name="title"
                    value={this.state.activeItem.title}
                    onChange={this.handleChange}
                    placeholder="Enter Todo Title"
                  />
                </FormGroup>
                <FormGroup>
                  <Label for="description">Description</Label>
                  <Input
                    type="text"
                    name="description"
                    value={this.state.activeItem.description}
                    onChange={this.handleChange}
                    placeholder="Enter Todo description"
                  />
                </FormGroup>
                <FormGroup check>
                  <Label for="completed">
                    <Input
                      type="checkbox"
                      name="completed"
                      checked={this.state.activeItem.completed}
                      onChange={this.handleChange}
                    />
                    Completed
                  </Label>
                </FormGroup>
              </Form>
            </ModalBody>
            <ModalFooter>
              <Button color="success" onClick={() => onSave(this.state.activeItem)}>
                Save
              </Button>
            </ModalFooter>
          </Modal>
        );
      }
    }
```

We created a `CustomModal` class and it nests the Modal component that is derived from the `reactstrap` library. We also defined three fields in the form:

- Title
- Description
- Completed

> These are the same fields that we defined as properties on the Todo model in the backend.

Here’s how the `CustomModal` works, it receives `activeItem`, `toggle`  and onSave as props.

1. `activeItem`  represents the Todo item to be edited.
2. `toggle` is a function used to control the Modal’s state i.e open or close the modal.
3. `onSave` is a function that is called to save the edited values of the Todo item.

Next, we will import the `CustomModal` component into the `App.js` file. Head over to the `src/App.js` file and replace it completely with this updated version:

```jsx
  // frontend/src/App.js

    import React, { Component } from "react";
    import Modal from "./components/Modal";

    const todoItems = [
      {
        id: 1,
        title: "Go to Market",
        description: "Buy ingredients to prepare dinner",
        completed: true
      },
      {
        id: 2,
        title: "Study",
        description: "Read Algebra and History textbook for upcoming test",
        completed: false
      },
      {
        id: 3,
        title: "Sally's books",
        description: "Go to library to rent sally's books",
        completed: true
      },
      {
        id: 4,
        title: "Article",
        description: "Write article on how to use django with react",
        completed: false
      }
    ];
    class App extends Component {
      constructor(props) {
        super(props);
        this.state = {
          modal: false,
          viewCompleted: false,
          activeItem: {
            title: "",
            description: "",
            completed: false
          },
          todoList: todoItems
        };
      }
      toggle = () => {
        this.setState({ modal: !this.state.modal });
      };
      handleSubmit = item => {
        this.toggle();
        alert("save" + JSON.stringify(item));
      };
      handleDelete = item => {
        alert("delete" + JSON.stringify(item));
      };
      createItem = () => {
        const item = { title: "", description: "", completed: false };
        this.setState({ activeItem: item, modal: !this.state.modal });
      };
      editItem = item => {
        this.setState({ activeItem: item, modal: !this.state.modal });
      };
      displayCompleted = status => {
        if (status) {
          return this.setState({ viewCompleted: true });
        }
        return this.setState({ viewCompleted: false });
      };
      renderTabList = () => {
        return (
          <div className="my-5 tab-list">
            <span
              onClick={() => this.displayCompleted(true)}
              className={this.state.viewCompleted ? "active" : ""}
            >
              complete
            </span>
            <span
              onClick={() => this.displayCompleted(false)}
              className={this.state.viewCompleted ? "" : "active"}
            >
              Incomplete
            </span>
          </div>
        );
      };
      renderItems = () => {
        const { viewCompleted } = this.state;
        const newItems = this.state.todoList.filter(
          item => item.completed === viewCompleted
        );
        return newItems.map(item => (
          <li
            key={item.id}
            className="list-group-item d-flex justify-content-between align-items-center"
          >
            <span
              className={`todo-title mr-2 ${
                this.state.viewCompleted ? "completed-todo" : ""
              }`}
              title={item.description}
            >
              {item.title}
            </span>
            <span>
              <button
                onClick={() => this.editItem(item)}
                className="btn btn-secondary mr-2"
              >
                Edit
              </button>
              <button
                onClick={() => this.handleDelete(item)}
                className="btn btn-danger"
              >
                Delete
              </button>
            </span>
          </li>
        ));
      };
      render() {
        return (
          <main className="content">
            <h1 className="text-white text-uppercase text-center my-4">Todo app</h1>
            <div className="row ">
              <div className="col-md-6 col-sm-10 mx-auto p-0">
                <div className="card p-3">
                  <div className="">
                    <button onClick={this.createItem} className="btn btn-primary">
                      Add task
                    </button>
                  </div>
                  {this.renderTabList()}
                  <ul className="list-group list-group-flush">
                    {this.renderItems()}
                  </ul>
                </div>
              </div>
            </div>
            {this.state.modal ? (
              <Modal
                activeItem={this.state.activeItem}
                toggle={this.toggle}
                onSave={this.handleSubmit}
              />
            ) : null}
          </main>
        );
      }
    }
    export default App;
```

We can now revisit the React frontend, this is what the application should resemble at this point:

![img](https://scotch-res.cloudinary.com/image/upload/v1542489453/ow5mzfinqpdtulsrnu0m.png)

If we attempt to edit and save a Todo item, we will get an alert  showing the Todo item’s object. Clicking on save, and delete will  perform the fitting actions on the Todo item.

We will now modify the application so that it interacts with the  Django API we built in the previous section. Let’s start by starting up  the backend server (on a different instance of the terminal) if it isn’t already running:

> Note: This command has to be run in the `backend` directory in a virtual Pipenv shell.

For us to make requests to the API endpoints on the backend server, we will install a JavaScript library called `axios.` Let’s pull in `axios using Yarn:

Once `axios` is successfully installed, head over to the `frontend/package.json` file and add a proxy like so:

The proxy will help in tunnelling API requests to [http://localhost:8000](http://localhost:8000/) where the Django application will handle them, so we can write the requests like this in the frontend:

```jsx
axios.get("/api/todos/")
```

Instead of this:

```jsx
axios.get("http://localhost:8000/api/todos/")
```

> Note: You might need to restart the development server for the proxy to register with the application.

We will modify the `frontend/src/App.js` one last time so  that it doesn’t use the hardcoded items from the array anymore, but  requests data from the backend server and lists them instead. We want to also ensure that all CRUD operations send requests to the backend  server instead of interacting with the dummy data.

Open the file and replace it with this final version:

```jsx
 // frontend/src/App.js

    import React, { Component } from "react";
    import Modal from "./components/Modal";
    import axios from "axios";

    class App extends Component {
      constructor(props) {
        super(props);
        this.state = {
          viewCompleted: false,
          activeItem: {
            title: "",
            description: "",
            completed: false
          },
          todoList: []
        };
      }
      componentDidMount() {
        this.refreshList();
      }
      refreshList = () => {
        axios
          .get("http://localhost:8000/api/todos/")
          .then(res => this.setState({ todoList: res.data }))
          .catch(err => console.log(err));
      };
      displayCompleted = status => {
        if (status) {
          return this.setState({ viewCompleted: true });
        }
        return this.setState({ viewCompleted: false });
      };
      renderTabList = () => {
        return (
          <div className="my-5 tab-list">
            <span
              onClick={() => this.displayCompleted(true)}
              className={this.state.viewCompleted ? "active" : ""}
            >
              complete
            </span>
            <span
              onClick={() => this.displayCompleted(false)}
              className={this.state.viewCompleted ? "" : "active"}
            >
              Incomplete
            </span>
          </div>
        );
      };
      renderItems = () => {
        const { viewCompleted } = this.state;
        const newItems = this.state.todoList.filter(
          item => item.completed === viewCompleted
        );
        return newItems.map(item => (
          <li
            key={item.id}
            className="list-group-item d-flex justify-content-between align-items-center"
          >
            <span
              className={`todo-title mr-2 ${
                this.state.viewCompleted ? "completed-todo" : ""
              }`}
              title={item.description}
            >
              {item.title}
            </span>
            <span>
              <button
                onClick={() => this.editItem(item)}
                className="btn btn-secondary mr-2"
              >
                {" "}
                Edit{" "}
              </button>
              <button
                onClick={() => this.handleDelete(item)}
                className="btn btn-danger"
              >
                Delete{" "}
              </button>
            </span>
          </li>
        ));
      };
      toggle = () => {
        this.setState({ modal: !this.state.modal });
      };
      handleSubmit = item => {
        this.toggle();
        if (item.id) {
          axios
            .put(`http://localhost:8000/api/todos/${item.id}/`, item)
            .then(res => this.refreshList());
          return;
        }
        axios
          .post("http://localhost:8000/api/todos/", item)
          .then(res => this.refreshList());
      };
      handleDelete = item => {
        axios
          .delete(`http://localhost:8000/api/todos/${item.id}`)
          .then(res => this.refreshList());
      };
      createItem = () => {
        const item = { title: "", description: "", completed: false };
        this.setState({ activeItem: item, modal: !this.state.modal });
      };
      editItem = item => {
        this.setState({ activeItem: item, modal: !this.state.modal });
      };
      render() {
        return (
          <main className="content">
            <h1 className="text-white text-uppercase text-center my-4">Todo app</h1>
            <div className="row ">
              <div className="col-md-6 col-sm-10 mx-auto p-0">
                <div className="card p-3">
                  <div className="">
                    <button onClick={this.createItem} className="btn btn-primary">
                      Add task
                    </button>
                  </div>
                  {this.renderTabList()}
                  <ul className="list-group list-group-flush">
                    {this.renderItems()}
                  </ul>
                </div>
              </div>
            </div>
            {this.state.modal ? (
              <Modal
                activeItem={this.state.activeItem}
                toggle={this.toggle}
                onSave={this.handleSubmit}
              />
            ) : null}
          </main>
        );
      }
    }
    export default App;
```

The `refreshList()` function is reusable that is called  each time an API request is completed. It updates the Todo list to  display the most recent list of added items.

The `handleSubmit()` function takes care of both the  create and update operations. If the item passed as the parameter  doesn’t have an id, then it has probably not been created, so the  function creates it.

Congratulations! We have just built the fontend successfully.

## Testing the application

Let’s start the backend server on a terminal instance that’s sourced  into the Pipenv virtual shell and pointed to the backend directory:

We also need to start the frontend development server:

We can visit the application on this address — [http://localhost:3000](http://localhost:3000/) — to see that it works:

![img](https://scotch-res.cloudinary.com/image/upload/v1542489918/v5csbtpcmkh2ajrihl1j.gif)

We’ve come to the end of this tutorial and learnt how to configure  Django and React to interact correctly with each other. We also saw some of the benefits that come with bootstrapping a React application using  the `create-react-app` tool, such as Hot-reloading which is  basically the feature that makes it possible for the web app to reload  on its own whenever a change is detected.

The source code for this tutorial is available [here](https://github.com/do-community/django-todo-react) on GitHub.
