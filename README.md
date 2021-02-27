# github-to-glitch-example

Creating a base setup for deploying React projects through github to glitch.

## Available Scripts

In the project directory, you can run:

### `npm start`

Runs the app in the development mode.\
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

The page will reload if you make edits.\
You will also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can’t go back!**

If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.

You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Setup

The following instructions have been copied directly from [Automating My Deploys From GitHub to Glitch by Melissa McEwen](https://dev.to/glitch/automating-my-deploys-from-github-to-glitch-2fpd)

### Step 1: In the Glitch terminal

Go to the Glitch terminal
Run `git config receive.denyCurrentBranch ignore`
create a githook in the terminal using your favorite text editor. I used Vim so `vim .git/hooks/post-receive`
Put this bash script into your hook:

```sh
#!/bin/bash
unset GIT_INDEX_FILE
git --work-tree=/app  --git-dir=/app/.git checkout -f
```

Give your hook execution permission `chmod +x .git/hooks/post-receive`

### Step 2: Create a GitHub secret

Head back to your Glitch project and click tools --> Git, Import, and Export
Copy Your project's Git URL: this contains an auth token so keep it secret!
Since it's a secret head to your Github repo and to the "secrets" section
Paste the whole thing into a new secret and name it `glitch_git_URL`

### Step 3: Create a GitHub Action

Head to actions and create a new workflow from "Set up a workflow yourself"
This is the code for using the git-sync action with your secret. Replace the value in SOURCE_REPO with your https GitHub URL (something like `https://github.com/glitchdotcom/devto.git`).

```yaml
on:
 pull_request:
  types: [closed]
jobs:
  repo-sync:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
    - name: repo-sync
      uses: wei/git-sync@v1
      env:
        SOURCE_REPO: "$GITHUB_SERVER_URL/$GITHUB_REPOSITORY.git"
        SOURCE_BRANCH: "main"
        DESTINATION_REPO: ${{ secrets.glitch_git_URL }}
        DESTINATION_BRANCH: "master"
      with:
        args: $SOURCE_REPO $SOURCE_BRANCH $DESTINATION_REPO $DESTINATION_BRANCH
```

### Step 4: Test it!

Now for the magic moment. Update your GitHub code anyway you choose. And click on Actions to see it in ...action...
