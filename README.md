# Martian Gallery

![](https://res.cloudinary.com/saurabhdaware/image/upload/c_scale,h_768,w_1366/v1545342565/saurabhdaware.in/apps.saurabhdaware.in/Screenshot_217.png)

## Introduction

Gallery of raw images returned by Curiosity, This project fetches images from NASA Mars rover API and groups the similar photos to create a video out of it. This is a simple quick project that I thought of making for the better visualization of the data

In this project I've used [VueJS](https://vuejs.org) and [NASA Mars Photos API](https://api.nasa.gov/api.html#MarsPhotos)


## Future Updates

- I am looking to add machine learning to create better groups, currently I am just checking image sizes and grouping them as per their size.
- Another thing that I am looking forward to add is to color the images using machine learning (maybe we can pass InSight's images as a dataset and color the pixels matching the colors of InSight's images)
- Images from InSight (currently there is no open API to do this :( )


## Known Issues

- When there are more than 30 pictures on a page. I only create video of first 30 images but I actually want to create video of all images when user clicks on load more button.

## Contributions

This project is open for new ideas and contributions just read [Contribution Guidelines](CONTRIBUTING.md) to start contributing :)


## Build Setup

```
# Clone repository
git clone https://github.com/saurabhdaware/martian-gallery

# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).
