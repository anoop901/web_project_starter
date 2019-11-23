This repo contains instructions on how to create a full stack application
(frontend and API server), using a set of configuration that I like.

# Setup instructions

Make sure you have `node` and `npm` installed.

Download this repo. Alternatively, create directory and create two empty
subdirectories in it: `frontend/` and `api/`.

Follow setup instructions in `frontend/README.md`.

Follow setup instructions in `api/README.md`.

Each of them can be built for production by `cd`-ing to their respective folder
and running this command:
```
npm run build
```

The API server will serve both the API and the frontend code. To run it, `cd`
to `api/` and run this command:
```
npm start
```

If you follow the instructions to set up devservers, you can simultaneously run
the following command in both `frontend/` and `api/`. This will update the UI
and server as you make changes to the code.
```
npm run devserver
```