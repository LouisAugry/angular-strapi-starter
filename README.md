# Welcome to angular-strapi-starter

This repository is a starter with **Angular** for the front end and **Strapi** for the back end. You can easily deploy these applications on **Netlify** for Angular and **Heroku** for Strapi. To do this, please follow the instructions below

# Clone the repository

First, clone the repository inside your local folder and install dependencies

    git clone https://github.com/LouisAugry/angular-strapi-starter.git
    cd apps/backend
    npm install
    cd ../frontend
    npm install


# Deploy Strapi to Heroku

Follow the instructions below to deploy Strapi to Heroku. In this repository, the sgbd is **PostgreSQL**

## Build the server on production mode
**Path —**  `./apps/backend`

Before running your server in production you need to build you admin panel for production

    NODE_ENV=production npm run build

## Heroku Install Requirements
This is a step-by-step guide for deploying a Strapi project on  [Heroku](https://www.heroku.com/). Databases that work well with Strapi and Heroku are provided instructions on how to get started.

You must have a  [free Heroku account](https://signup.heroku.com/)  before doing these steps.

If you already have the Heroku CLI installed locally on your computer. Skip to  [Login to Heroku]

### 1. Heroku CLI Installation

##### macOS
[Download the installer](https://cli-assets.heroku.com/heroku.pkg)

Also available via Homebrew:

```
brew tap heroku/brew && brew install heroku
```
---------------

##### Ubuntu
Run the following from your terminal:

```
sudo snap install --classic heroku
```
----------

###### Windows
Download the appropriate installer for your Windows installation:

[64-bit installer](https://cli-assets.heroku.com/heroku-x64.exe)  [32-bit installer](https://cli-assets.heroku.com/heroku-x86.exe)

------
### 2. Login to Heroku from your CLI

Next, you need to login to Heroku from your computer.

```
heroku login
```

Follow the instructions and return to your command line.

### 3. Update  `.gitignore`

Add the following line at end of  `.gitignore`:

`Path: ./apps/backend/.gitignore`

```
package-lock.json
```

Even if it is usually recommended to version this file, it may create issues on Heroku.

### 4. Commit your project

Commit your project to your repository.
```
git add .
git commit -am "Initial Commit"
```

### 5. Create a Heroku project

Create a new Heroku project.

`Path: ./apps/backend`

```
heroku create
```

(You can use  `heroku create custom-project-name`, to have Heroku create a  `custom-project-name.heroku.com`  URL. Otherwise, Heroku will automatically generating a random project name (and URL) for you.)

> NOTE
>
> If you have a Heroku project app already created. You would use the
> following step to initialize your local project folder:
>
> `Path: ./apps/backend`
>
> ```
> heroku git:remote -a your-heroku-app-name
> ```

Your local development environment is now set-up and configured to work with Heroku. You have a new Strapi project and a new Heroku app ready to be configured to work with a database and with each other.

### 6. Heroku Database set-up

Follow these steps to deploy your Strapi app to Heroku using  **PostgreSQL**:

##### 1. Install the  [Heroku Postgres addon](https://elements.heroku.com/addons/heroku-postgresql)  for using Postgres.

To make things even easier, Heroku provides a powerful addon system. In this section, you are going to use the Heroku Postgres addon, which provides a free "Hobby Dev" plan. If you plan to deploy your app in production, it is highly recommended to switch to a paid plan.

`Path: ./apps/backend`

```
heroku addons:create heroku-postgresql:hobby-dev
```

##### 2. Retrieve database credentials

The add-on automatically exposes the database credentials into a single environment variable accessible by your app. To retrieve it, type:

`Path: ./apps/backend`

```
heroku config
```

This should print something like this:
`DATABASE_URL: postgres://ebitxebvixeeqd:dc59b16dedb3a1eef84d4999sb4baf@ec2-50-37-231-192.compute-2.amazonaws.com: 5432/d516fp1u21ph7b`.

(This url is read like so: *postgres://  **USERNAME**  :  **PASSWORD**  @  **HOST**  :  **PORT**  :  **DATABASE_NAME***)

##### 3. Set environment variables

Strapi expects a variable for each database connection configuration (host, username, etc.). So, from the url above, you have to set several environment variables in the Heroku config:

```
heroku config:set DATABASE_USERNAME=ebitxebvixeeqd
heroku config:set DATABASE_PASSWORD=dc59b16dedb3a1eef84d4999a0be041bd419c474cd4a0973efc7c9339afb4baf
heroku config:set DATABASE_HOST=ec2-50-37-231-192.compute-2.amazonaws.com
heroku config:set DATABASE_PORT=5432
heroku config:set DATABASE_NAME=d516fp1u21ph7b
```

**Note:**  Please replace these above values with the your actual values.

##### 4. Update your database config file

Replace the contents of  `database.json`  with the following:

`Path: .apps/backend/config/environments/production/database.json`.

```
{
  "defaultConnection": "default",
  "connections": {
    "default": {
      "connector": "strapi-hook-bookshelf",
      "settings": {
        "client": "postgres",
        "host": "${process.env.DATABASE_HOST}",
        "port": "${process.env.DATABASE_PORT}",
        "database": "${process.env.DATABASE_NAME}",
        "username": "${process.env.DATABASE_USERNAME}",
        "password": "${process.env.DATABASE_PASSWORD}",
        "ssl": true
      },
      "options": {}
    }
  }
}
```

##### 5. Install the  `pg`  node module

Unless you originally installed Strapi with PostgreSQL, you need to install the  [pg](https://www.npmjs.com/package/pg)  node module.

`Path: ./apps/backend`

```
npm install pg --save
```

### 7. Commit your changes

```
git commit -am "Update database config"
```

### 8. Deploy

`Path: ./apps/backend`

```
git push heroku master
```

The deployment may take a few minutes. At the end, logs will display the url of your project (e.g.  `https://mighty-taiga-80884.herokuapp.com`). You can also open your project using the command line:

`Path: ./apps/backend`

```
heroku open
```

If you see the Strapi Welcome page, you have correctly set-up, configured and deployed your Strapi project on Heroku. You will now need to set-up your  `admin user`  as the production database is brand-new (and empty).

You can now continue with the  [Tutorial - Creating an Admin User](https://strapi.io/documentation/3.0.0-beta.x/getting-started/quick-start-tutorial.html#_3-create-an-admin-user), if you have any questions on how to proceed.

> NOTE
>
> For security reasons, the Content Type Builder plugin is disabled in
> production. To update content structure, please make your changes
> locally and deploy again.

### 9. Project updates

When Strapi is deployed to Heroku, Heroku sets the environment variable to  `NODE_ENV=production`. In  `production mode`  Strapi disables the content-type builder (for security reasons). Additionally, if you wanted to change the default production mode in Heroku, it wouldn't work as the file system is temporary. Strapi writes files to the server when you update the content-types and these updates would disappear when Heroku restarts the server.

Therefore, modifications that require writing to model creation or other json files, e.g. creating or changing content-types, require that you make those changes on your dev environment and then push the changes to Heroku.

As you continue developing your application with Strapi, you may want to use  [version control](https://devcenter.heroku.com/articles/github-integration), or you can continue to use  `Git push heroku master`  to commit and push changes to Heroku directly.

`Path: ./apps/backend`

```
git add .
git commit -am "Changes to my-project noted"
git push heroku master
heroku open
```
