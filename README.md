# layer-swiper

> cart can swiper in layer

## install

``` bash
# install dependencies
npm install layer-swiper
```

## What is layer-swiper?
  layer-swiper is plugin by which three cart override can scroll by finger or mouse ,when you let it go and three cart will go where they should go .how you use it? you just put three dom in components and it will works.what's more,there is lots of property that you can custom it byself.
## Getting started
  we can use it by follow and show your imgs or carts in softly,and you can write anything in div or li.
  ```
   <LayerSwiepr>
     <div>1</div>
     <div>2</div>
     <div>3</div>
   </LayerSwiper>

   
   <script>
    import LayerSwiper from 'layer-swiper'
   </script>
   ```
## what is we can achieve as follows;
[show](https://github.com/XiaoQueXinggg/layer-swiper/blob/master/src/assets/show.gif)
## Could we use it in which platforms?
  Nowadays,it can't supprt under ie 10,and support other brower,
  it could support mobile
## Usage
  a_pos default 3 // The position of the first cart
  b_pos default 28 //The position of the second cart
  c_pos default 53 //The position of the third cart
  cur_rate default 1 //The rate the distance that you finger or mouse move devide the distance that the cart move 
  scale default 1.25 //The times that middle cart scale 
  opacity default 0.7 //set the side cart opacity 
  noreact default 1 // when we scroll down,we could free it casually. so you can controll the react by set the value.the more value you set, you can't free it more easily
  platform default pc //which platform you want use it, or 'mobile'
  top default 25 //the distance between the side cart and top
