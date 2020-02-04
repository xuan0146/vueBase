<template>
    <div class="canvasPage cp">
        <div class="cp_box" ref="boardBox">
            <canvas ref="board"
                    @mousedown="pcStart"
                    @mousemove="pcMove"
                    @mouseup="pcEnd"
                    @touchstart="mStart"
                    @touchmove="mMove"
                    @touchend="mEnd">
            </canvas>
        </div>
        <div class="cp_btn">
            <el-button type="danger" @click="cleanCanvas">清空</el-button>
            <el-button type="success" @click="saveCanvas">保存</el-button>
        </div>
        <el-card v-if="base64">
            <img :src="base64" alt="">
        </el-card>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                ctx: null,
                width: 0,
                height: 0,
                base64: '',
                point: {
                    x: 0,
                    y: 0
                },
                moving: false   // 是否正在绘制中且移动
            };
        },
        mounted() {
            let board = this.$refs.board;   // 获取DOM
            this.width = board.width = this.$refs.boardBox.offsetWidth;  // 设置画布宽
            this.height = board.height = this.$refs.boardBox.offsetHeight;    // 设置画布高
            this.ctx = board.getContext('2d');   // 二维绘图
            this.ctx.strokeStyle = '#000';   // 颜色
            this.ctx.lineWidth = 3;  // 线条宽度
        },
        methods: {
            // 鼠标按下(开始)
            pcStart (e) {
                let x = e.offsetX, y = e.offsetY;   // 获取鼠标在画板（canvas）的坐标
                this.point.x = x;
                this.point.y = y;
                this.ctx.beginPath();
                this.moving = true;
            },
            // 鼠标移动（移动中...）
            pcMove (e) {
                if(this.moving) {
                    let x = e.offsetX, y = e.offsetY;   // 获取鼠标在画板（canvas）的坐标
                    this.ctx.moveTo(this.point.x, this.point.y);    // 把路径移动到画布中的指定点，不创建线条(起始点)
                    this.ctx.lineTo(x, y);  // 添加一个新点，然后创建从该点到画布中最后指定点的线条，不创建线条
                    this.ctx.stroke();  // 绘制
                    this.point.x = x, this.point.y = y;   // 重置点坐标为上一个坐标
                }
            },
            // 鼠标松开（结束）
            pcEnd () {
                if(this.moving) {
                    this.ctx.closePath();   // 停止绘制
                    this.moving = false;    // 关闭绘制开关
                }
            },
            // 触摸(开始)
            mStart (e) {
                console.log(e);
                let x = e.touches[0].clientX - e.target.offsetLeft,
                    y = e.touches[0].clientY - e.target.offsetTop;   // 获取触摸点在画板（canvas）的坐标
                this.point.x = x;
                this.point.y = y;
                this.ctx.beginPath();
                this.moving = true;
            },
            // 滑动中...
            mMove (e) {
                if(this.moving) {
                    let x = e.touches[0].clientX - e.target.offsetLeft,
                        y = e.touches[0].clientY - e.target.offsetTop;   // 获取触摸点在画板（canvas）的坐标
                    this.ctx.moveTo(this.point.x, this.point.y);    // 把路径移动到画布中的指定点，不创建线条(起始点)
                    this.ctx.lineTo(x, y);  // 添加一个新点，然后创建从该点到画布中最后指定点的线条，不创建线条
                    this.ctx.stroke();  // 绘制
                    this.point.x = x, this.point.y = y;   // 重置点坐标为上一个坐标
                }
            },
            // 滑动结束
            mEnd () {
                if(this.moving) {
                    this.ctx.closePath();   // 停止绘制
                    this.moving = false;    // 关闭绘制开关
                }
            },
            // 清空画布
            cleanCanvas () {
                // clearRect() 方法清空给定矩形内的指定像素。
                this.ctx.clearRect(
                    0,
                    0,
                    this.width,
                    this.height
                )

            },
            // 保存签名
            saveCanvas () {
                this.base64 = this.$refs.board.toDataURL(); // 转为base64
            }
        },
    };
</script>

<style lang="scss" scoped>
    .cp{
        padding: 20px;
        &_box{
            margin: 30px auto;
            width: 90vw;
            height: 20vh;
            background: #f9f9f9;
            canvas{
                border: 1px solid #b3b3b3;
            }
        }
        &_btn{
            margin: 20px 0;
        }
    }

</style>