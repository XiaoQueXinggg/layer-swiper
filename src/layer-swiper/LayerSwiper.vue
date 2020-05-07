<template>
  <div id="app">
   <div class="box"  
   >
     <div class="swiper-content"
     v-bind:class="platform=='pc'?'swiper-pc':'swiper-mobile'"
     ref="box"
     @touchstart="tstart_handle()"
      @touchmove="tmove_handle()"
      @touchend="tend_handle()"
     >
      <slot></slot>
	</div>
   </div>
  </div>
</template>

<script>
export default {
  name: 'LayerSwiper',
  data () {
    return {
      a:2,
      b:0,
      c:1,
      x:2,
      y:0,
      z:1,
      initX:0,
      initY:0,
      firstX:0,
      status:0,
      add_trans:false,
      scrollBox:'',
      target:'',
      rate:'',
      cart_back:false,
      scr_init_time:'',
      scr_end_time:'',
      scrolling:false,
      len:0,
    }
  },
  props:{
      cus_rate:{
        type:Number,
        default:1,
      },
      a_pos:{
        type:Number,
        default:3
      },
      b_pos:{
        type:Number,
        default:28
      },
      c_pos:{
        type:Number,
        default:53
      },
      scale:{
        type:Number,
        default:1.25
      },
      opacity:{
        type:Number,
        default:0.7
      },
      noreact:{
        type:Number,
        default:1
      },
      platform:{
        type:String,
        default:'pc'
      },
      top:{
        type:String,
        default:'25'
      }
  },
  watch:{
      add_trans(value){
          if(value){
            for(var i in this.scrollBox.children){
                if(!this.scrollBox.children[i].classList){
                  continue
                }
                this.scrollBox.children[i].classList.add('trans_poper')
            }
          }else{
            for(var i in this.scrollBox.children){
                if(!this.scrollBox.children[i].classList){
                  continue
                }
                this.scrollBox.children[i].classList.remove('trans_poper')
            }
          }
      }
  },
  methods:{
    tstart_handle(e){
      if(!e){
         return;
       }
      this.target=this.platform=='pc'?e:e.touches[0];
      this.initX=this.target.screenX;
			this.initY=this.target.screenY;
			this.firstX=this.target.screenX;
			this.scrolling=true;
			this.add_trans=false;
      this.scr_init_time=new Date().getTime();
      if(this.platform=='pc'){
        this.scrollBox.addEventListener('mousemove',this.tmove_handle,false)
        this.scrollBox.addEventListener('mouseup',this.tend_handle,false)
      }
    },
    tend_handle(e){
      if(this.cart_back){
				return;
      }
      if(this.platform=='pc'){
        this.scrollBox.removeEventListener('mousemove',this.tmove_handle,false)
        this.scrollBox.removeEventListener('mouseup',this.tend_handle,false)
      }
			this.scr_end_time=new Date().getTime();
      this.add_trans=true;
			if(0.25*this.rate<0.04){
				[this.a,this.b,this.c]=[this.x,this.y,this.z];
      }
      this.scrollBox.children[this.a]['style']['left']=this.c_pos+'%';
			this.scrollBox.children[this.a]['style']['transform']='scale(1)';
			this.scrollBox.children[this.a]['style']['opacity']=this.opacity;
			this.scrollBox.children[this.a]['style']['z-index']='1';
			this.scrollBox.children[this.b]['style']['left']=this.a_pos+'%';
			this.scrollBox.children[this.b]['style']['opacity']=this.opacity;
			this.scrollBox.children[this.b]['style']['transform']='scale(1)';
			this.scrollBox.children[this.b]['style']['z-index']='2';
			this.scrollBox.children[this.c]['style']['left']=this.b_pos+'%';
			this.scrollBox.children[this.c]['style']['opacity']=1;
			this.scrollBox.children[this.c].children[0]['style']['opacity']=1;
			this.scrollBox.children[this.c]['style']['z-index']='3';
			this.scrollBox.children[this.c]['style']['transform']='scale('+this.scale+')';
      this.scrollBox.children[this.c].children[0]['style']['opacity']=1;
			if(0.25*this.rate>0.04){
				this.x=this.a;
				this.y=this.b;
				this.z=this.c;
			}
      this.status=0;
    },
    tmove_handle(e){
      if(!e){
         return;
       }
      this.target=this.platform=='pc'?e:e.touches[0];
      var dis,abs_dis;
      if(Math.abs((this.target.screenX-this.initX))/Math.abs((this.target.screenY-this.initY))<this.noreact
      ||Math.abs((this.target.screenY-this.initY))/Math.abs((this.target.screenX-this.initX))==Infinity){
				this.cart_back=true;
				return;
      }
			this.cart_back=false;
			dis=this.target.screenX-this.firstX;
			abs_dis=Math.abs(dis);
      this.rate=abs_dis/this.$refs['box'].offsetWidth*this.cus_rate;
			this.add_trans=false;
			if(this.target.screenX-this.initX<0){
				if(this.scrolling){
					[this.a,this.b,this.c]=[this.b,this.c,this.a];
					this.scrolling=false;
					this.status=1;
        }
				if(this.status==2){
          [this.a,this.b,this.c]=[this.c,this.a,this.b];
					this.status=1;
				}else{
          this.scrollBox.children[this.a]['style']['left']=(this.a_pos+(this.c_pos-this.a_pos)*this.rate)+'%'
          this.scrollBox.children[this.b]['style']['transform']='scale('+(this.scale-(this.scale-1)*this.rate)+')';
          this.scrollBox.children[this.b]['style']['left']=(this.b_pos-(this.b_pos-this.a_pos)*this.rate)+'%';
          this.scrollBox.children[this.b]['style']['opacity']=1-(1-this.opacity)*this.rate;
          this.scrollBox.children[this.c]['style']['transform']='scale('+(1+this.rate*(this.scale-1))+')';
          this.scrollBox.children[this.c]['style']['opacity']=this.opacity+(1-this.opacity)*this.rate;
          this.scrollBox.children[this.c]['style']['left']=this.c_pos-(this.b_pos*this.rate)+'%';
				}
			}else{
				if(this.scrolling){
					[this.a,this.b,this.c]=[this.c,this.a,this.b];
					this.scrolling=false;
					this.status=2;
				}
				if(this.status==1){
          [this.a,this.b,this.c]=[this.b,this.c,this.a];
            this.status=2
        } 
        this.scrollBox.children[this.a]['style']['left']=this.b_pos+(this.b_pos-this.a_pos)*this.rate+'%';;
        this.scrollBox.children[this.a]['style']['transform']='scale('+(this.scale-(this.scale-1)*this.rate)+')';
        this.scrollBox.children[this.a]['style']['opacity']=1-(1-this.opacity)*this.rate;
        this.scrollBox.children[this.b]['style']['left']=(this.c_pos-(this.c_pos-this.a_pos)*this.rate)+'%';
        this.scrollBox.children[this.c]['style']['transform']='scale('+(1+(this.scale-1)*this.rate)+')';
        this.scrollBox.children[this.c]['style']['opacity']=this.opacity+(1-this.opacity)*this.rate;
        this.scrollBox.children[this.c]['style']['left']=(this.a_pos+(this.b_pos-this.a_pos)*this.rate)+'%';
			}
			if(0.25*this.rate>0.04){
				this.scrollBox.children[this.a]['style']['z-index']='1';
				this.scrollBox.children[this.b]['style']['z-index']='2';
				this.scrollBox.children[this.c]['style']['z-index']='3';
			}else{
				this.scrollBox.children[this.x]['style']['z-index']='1';	
				this.scrollBox.children[this.y]['style']['z-index']='2';
				this.scrollBox.children[this.z]['style']['z-index']='3';
      }
      if(this.platform=='pc'&&e.which==0){
        this.tend_handle()
      }
    }
  },
  mounted(){
    this.scrollBox=this.$refs['box'];
    for(var i in this.scrollBox.children){
      if(i<3){
        this.scrollBox.children[i].classList.add('swiper-child'+i);
        if(i==0){
          this.scrollBox.children[i]['style']['left']=this.a_pos+'%';
          this.scrollBox.children[i]['style']['position']='absolute';
          this.scrollBox.children[i]['style']['top']=this.top+'px';
        }else if(i==1){
          this.scrollBox.children[i]['style']['left']=this.b_pos+'%';
          this.scrollBox.children[i]['style']['position']='absolute';
          this.scrollBox.children[i]['style']['transform']='scale('+this.scale+')';
          this.scrollBox.children[i]['style']['opacity']=1;
          this.scrollBox.children[i]['style']['top']=this.top+'px';
          this.scrollBox.children[i]['style']['z-index']=2;
        }else if(i==2){
          this.scrollBox.children[i]['style']['left']=this.c_pos+'%';
          this.scrollBox.children[i]['style']['position']='absolute';
          this.scrollBox.children[i]['style']['top']=this.top+'px';
        }
      }else{
        if(!this.scrollBox.children[i].classList){
          continue
        }
        this.scrollBox.children[i].classList.add('swiper-childno');
      }
    }
    this.len=this.scrollBox.children.length;
    if(this.platform!='pc'){
      this.scrollBox.addEventListener('touchstart',this.tstart_handle,false)
      this.scrollBox.addEventListener('touchmove',this.tmove_handle,false)
      this.scrollBox.addEventListener('touchend',this.tend_handle,false)
    }else{
      this.scrollBox.addEventListener('mousedown',this.tstart_handle,false)
      this.scrollBox.onselectstart = function(event){
        event.returnValue = false;
      }
      document.addEventListener('mouseup',(e)=>{
        this.tend_handle(e)
      },false)
    }
    
  }
}
</script>

<style scoped>
#app{
  height:100%;
  width:100%;
}
.box{
  height:100%;
  width:100%;
}
.trans_poper{
  -moz-transition:left 300ms ease-in-out,transform 300ms ease-in-out,top 300ms ease-in-out;
  -ms-transition:left 300ms ease-in-out,transform 300ms ease-in-out,top 300ms ease-in-out;
  -webkit-transition:left 300ms ease-in-out,transform 300ms ease-in-out,top 300ms ease-in-out;
  -o-transition:left 300ms ease-in-out,transform 300ms ease-in-out,top 300ms ease-in-out;
  transition:left 300ms ease-in-out,transform 300ms ease-in-out,top 300ms ease-in-out;
}
.swiper-content{
	position: relative;
	margin:0px auto;
}
.swiper-pc{
	width:30%;
  padding-bottom:40%;
}
.swiper-mobile{
  width:100%;
  padding-bottom: 50%;
}
.swiper-child1{
  width:40%;
  padding-bottom:40%;
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.1);	
}
  .swiper-child0{
  width:40%;
  padding-bottom:40%;
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.1);	
}
.swiper-child2{
  width:40%;
  padding-bottom:40%;
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.1);	
}
.swiper-childno{
  display: none;
  width:0px;
  padding-bottom:0px;
  position:absolute;
  box-shadow: 0px 0px 10px 0px rgba(0, 0, 0, 0.1);	
}
</style>
