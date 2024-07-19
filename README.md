# play-from-link

## Description

A simple web app made in Vue for playing WMA audio files from a provided link. \
It fetches the needed audio file by a CORS proxy (thanks to cors-anywhere), then converts it to MP3 using FFMPEG.wasm. \
I needed it to play WMA audio files that browsers do not normally support.

## CORS proxy

Please keep in mind, that you should host your own CORS proxy if possible. \
See https://github.com/Rob--W/cors-anywhere for more details. \
If you want to use the demo version, you first need to activate it on https://cors-anywhere.herokuapp.com.


## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```
