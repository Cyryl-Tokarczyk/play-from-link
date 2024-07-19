# play-from-link

## Description

A simple web app made in Vue for playing WMA audio files from a provided link. \
It fetches the needed audio file by a CORS proxy (thanks to cors-anywhere), then converts it to MP3 using FFMPEG.wasm. \
I needed it to play WMA audio files that browsers do not normally support.

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
