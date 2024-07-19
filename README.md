# play-from-link

## Description

A simple web-app made for playing WMA audio files from a provided link. \
It fetches the needed audio file by a CORS proxy (thanks to cors-anywhere), then converts the file to MP3 using FFMPEG.wasm. \
I needed it to play WMA audio files which are not normally supported by browsers.

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