# Check whether installed node version includes npx or not

`which npx`

## if it is not included, install it

    `npm install -g npx`

# Run a locally install package easily

`npx your-package`

# Execute packages that are not previously installed

`npx https://gist.github.com/XXX/yourexampleremotescriptlocation`

# Test different package versions

## List dist tags

    `npm v your-package`

## install it inside a sandbox

    `npmx your-package@next sandbox`

## run the sandbox version inside its directory.

    `cd sandbox`
    `npm start`

### Ref

https://www.freecodecamp.org/news/npm-vs-npx-whats-the-difference/
