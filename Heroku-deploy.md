# Deploying to Heroku from command line

1. Add SSH key to heroku account

   `heroku keys:add`

1. Create heroku project. (will get a url of project name)

   `heroku create abc-application`

1. Add start script in package.json so that heroku can know which file to run

   ```
   "script": {
     "start": "node src/app.js"
   }
   ```

1. Add dynamic port value to listen in app which is provided by heroku

   `const port = process.env.PORT || localhostport`

   \*localhostport is used in dev environment, eg: 3000

1. Push code to heroku

   `git push heroku master`
