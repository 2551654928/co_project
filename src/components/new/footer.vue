<template>
    <div>
        <div>
            <footer class="desk-footer">
                <img src="../../assets/images/turnTable/bgd_03.png" alt="" title="" class="desk-footer-img"/>
                <div class="desk-box-change">
                    <div class="desk-progressbar" value="0"><em style="width:0%;"></em></div>
                    <a @click="increaseClick" href="javascript:;" class="desk-progressbar-cut">-</a>
                    <a @click="reduceClick" href="javascript:;" class="desk-progressbar-add">+</a>
                    <span class="desk-box-name">返点:<span>0%</span></span>
                    <span class="desk-box-left">0%</span>
                    <span class="desk-box-right">8%</span>
                </div>
                <a href="#" class="desk-footer-left"><img src="../../assets/images/turnTable/btnd_01.png" alt=""
                                                          title=""/></a>
                <a href="javascript:;" class="desk-footer-right"><img src="../../assets/images/turnTable/btnd_02.png"
                                                                      alt="" title=""/></a>
                <ul class="desk-currency">
                    <li @click="marryClick(0, 1)"><img src="../../assets/images/turnTable/currency_01.png" alt="" title=""></li>
                    <li @click="marryClick(0, 10)" class="on"><img src="../../assets/images/turnTable/currency_02.png" alt="" title=""></li>
                    <li @click="marryClick(0, 100)"><img src="../../assets/images/turnTable/currency_03.png" alt="" title=""></li>
                    <li @click="marryClick(0, 1000)"><img src="../../assets/images/turnTable/currency_04.png" alt="" title=""></li>
                </ul>
                <input type="hidden" value="../../assets/images/turnTable/currency_02.png" class="desk-currency-hide"/>
            </footer>
            <footer class="desk-footer-other" style="display:none;">
                <img src="../../assets/images/turnTable/bgd_02.png" alt="" title="" class="desk-footer-a"/>
                <a href="#" class="desk-footer-l">清空</a>
                <a @click="onConfirmBtnClicked()" href="#" class="desk-footer-r">确认投注</a>
                <p class="desk-footer-con">共4注 <span>4元</span></p>
            </footer>
        </div>

    </div>
</template>
<script type="text/babel">
  import $ from 'jquery'

  export default {
	props: {
	  personalRebate: {default: ''},
	  GdlotteryDataArr: {default: ''},
	  curtwoIndex: {default: ''},
	  creditId: {default: ''}
	},
	data() {
	  return {
		marryImgId: 1,
		marryImg: [
		  {img: require('../../assets/images/marry_1.png'), num: 1, trueEd: false},
		  {img: require('../../assets/images/marry_10.png'), num: 10, trueEd: false},
		  {img: require('../../assets/images/marry_100.png'), num: 100, trueEd: false},
		  {img: require('../../assets/images/marry_1000.png'), num: 1000, trueEd: false}
		],
		sliderValue: 0,
		dragMan_maxline: null, //灰色
		dragMan_minline: null,
		dragMan_redLine: null, //color
		liu_codeMun_lodds: null,
		liu_codeMun_lodds_span: null,
		draging: false,
		dragMan_minline_width: 20,
		dragminLeft: 171,
		offsetWidth: 0,
		bonus: 0,
		multiple: '', // 官方玩法：倍数   信用玩法：金额
		timeSet: 0
	  }
	},
	computed: {},
	watch: {
	  'creditId': function () {
		var _this = this;
		setTimeout(function () {
		  //_this.initSlider();
		}, 200)
	  }
	},
	mounted() {
	  //this.initSlider();
      let that = this;
	  //加减
	  $('.desk-progressbar-add').click(function () {
		if ($('.desk-progressbar').attr('value') < 10) {
		  $(this).siblings('.desk-progressbar').attr('value', (parseInt($(this).siblings('.desk-progressbar').attr('value')) + 1));
		  $(".desk-box-name span").html($('.desk-progressbar').attr('value') + "0%");
		  $(".desk-progressbar em").css("width", $('.desk-progressbar').attr('value') + "0%");
		  that.$emit('sliderValue', this.sliderValue)
		}
	  });
	  $('.desk-progressbar-cut').click(function () {
		if ($('.desk-progressbar').attr('value') > 0) {
		  $(this).siblings('.desk-progressbar').attr('value', (parseInt($(this).siblings('.desk-progressbar').attr('value')) - 1));
		  if ($('.desk-progressbar').attr('value') == 0) {
			$(".desk-box-name span").html($('.desk-progressbar').attr('value') + "%");
		  } else {
			$(".desk-box-name span").html($('.desk-progressbar').attr('value') + "0%");
		  }
		  $(".desk-progressbar em").css("width", $('.desk-progressbar').attr('value') + "0%");
		}
	  });

	  $(".desk-currency li").click(function () {
		$(this).addClass("on").siblings().removeClass();
		$(".desk-currency-hide").val($(this).find("img").attr("src"));
	  });

	},
	methods: {
	  resetData: function () {
		this.sliderValue = 0;
//        this.dragMan_maxline = null;
		this.dragMan_minline.style.left = '0px';
		this.dragMan_redLine.style.width = '0px';
//        this.dragMan_redLine = null;
//        this.liu_codeMun_lodds = null;
//        this.liu_codeMun_lodds_span = null;
//        this.draging = false;
//        this.dragMan_minline_width = 20;
//        this.dragminLeft = 0;
//        this.offsetWidth = 0;
		this.bonus = 0;
		this.multiple = ''; // 官方玩法：倍数   信用玩法：金额

//        this.resetSlider();
		this.marryImgId = 1;
		this.$parent.$emit('resetData', true);
	  },

	  marryClick: function (index, num) {
		this.marryImgId = index;
		this.$parent.$emit('marryNum', num);
	  },
	  increaseClick: function () {
		this.dragMan_getCalp('0');
	  },
	  reduceClick: function () {
		this.dragMan_getCalp('1');
	  },
	  /*点击获取位置*/
	  dragMan_getCalp: function (id) {
		if (this.liu_codeMun_lodds_span.innerHTML == "0") {
		  this.dragminLeft = 0;
		}
		this.offsetWidth = this.dragMan_maxline.offsetWidth - this.dragMan_minline_width;
		if (id == "1") {
		  this.dragminLeft += this.dragMan_maxline.offsetWidth / 8;
		  if (this.dragminLeft >= this.offsetWidth) {
			this.dragminLeft = this.offsetWidth;
		  }
		} else {
		  this.dragminLeft -= this.dragMan_maxline.offsetWidth / 8;
		  if (this.dragminLeft < 0) {
			this.dragminLeft = 0;
		  }

		}
		this.updateDotPos(this.dragminLeft, this.offsetWidth);
	  },
	  //滑动效果
	  // 事件
	  start: function (e) {
		e.stopPropagation();
		this.draging = true;
		// console.log("鼠标按下")
	  },
	  move: function (e) {
		e.stopPropagation();
		//console.log("鼠标拖动")
		if (this.draging) {
		  if (!e.touches) {
			//兼容移动端
			var x = e.clientX;
		  } else {
			//兼容PC端
			var x = e.touches[0].pageX;
		  }
		  var lineDiv_left = this.getPosition(this.dragMan_maxline).left;
		  this.dragminLeft = x - lineDiv_left;
		  this.offsetWidth = this.dragMan_maxline.offsetWidth - this.dragMan_minline_width;
		  if (this.dragminLeft >= this.offsetWidth) {
			this.dragminLeft = this.offsetWidth;
		  }
		  if (this.dragminLeft < 0) {
			this.dragminLeft = 0;
		  }
		  this.updateDotPos(this.dragminLeft, this.offsetWidth);
		}
	  },
	  end: function (e) {
		//console.log("鼠标弹起")

		this.draging = false;
	  },
	  //获取元素的绝对位置
	  getPosition: function (node) {
		var left = node.offsetLeft; //获取元素相对于其父元素的left值var left
		var top = node.offsetTop;
		var current = node.offsetParent; // 取得元素的offsetParent
		// 一直循环直到根元素
		while (current != null) {
		  left += current.offsetLeft;
		  top += current.offsetTop;
		  current = current.offsetParent;
		}
		return {
		  "left": left,
		  "top": top
		};
	  },
	  updateDotPos: function (val, wih) {
		this.dragMan_minline.style.left = val + "px";
		this.dragMan_redLine.style.width = val + 2 + "px";
		this.sliderValue = (parseFloat(((wih - (wih - val)) / wih) * this.personalRebate)).toFixed(0)

		this.liu_codeMun_lodds_span.innerText = this.sliderValue;
		this.$emit('sliderValue', this.sliderValue)
	  },
	  initSlider: function () {
		this.dragMan_maxline = document.getElementsByClassName("dragMan_maxline")[0], //灰色
			this.dragMan_minline = document.getElementsByClassName("dragMan_minline")[0]
		this.dragMan_redLine = document.getElementsByClassName("dragMan_redLine")[0] //color
		this.liu_codeMun_lodds = document.getElementsByClassName("liu_codeMun_lodds")[0]
		this.liu_codeMun_lodds_span = this.liu_codeMun_lodds.getElementsByTagName("span")[0]
		this.dragMan_minline.addEventListener("touchstart", this.start.bind(this))
		this.dragMan_minline.addEventListener("mousedown", this.start.bind(this))
		window.addEventListener("touchmove", this.move.bind(this))
		window.addEventListener("mousemove", this.move.bind(this))
		window.addEventListener("touchend", this.end.bind(this))
		window.addEventListener("mouseup", this.end.bind(this))
	  },
	  resetSlider: function () {
		this.offsetWidth = this.dragMan_maxline.offsetWidth - this.dragMan_minline_width
		this.dragminLeft = this.offsetWidth
		this.updateDotPos(this.dragminLeft, this.offsetWidth)
		this.multiple = this.isCredit ? '' : 1
		this.liu_codeMun_lodds_span.innerText = this.sliderValue
		this.$emit('sliderValue', this.sliderValue)
		this.$emit('multiple', this.multiple)
	  },
	  onConfirmBtnClicked: function () {
		this.$emit('confirmBtnClicked');
	  }
	}
  }
</script>

<style>

</style>
