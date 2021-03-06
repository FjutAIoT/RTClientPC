<template>
    <div class="view-complete">
        <div class="view-complete-content">
            <canvas class="canvas"
                    ref="canvas"
                    width="200"
                    height="200"
                    v-show="loading">
            </canvas>
            <svg-icon v-show="!loading"
                      class="view-complete-content-icon"
                      :iconClass="iconClass" />
        </div>
        <span class="view-complete-text">
            {{text}}
        </span>
        <div class="view-complete-user"
             v-show="!loading">
            <span>
                用户 ID
                <i>
                    {{user.userId}}
                </i>
            </span>
            <span>
                家庭组 ID
                <i>
                    {{user.groupId}}
                </i>
            </span>
        </div>
        <div class="view-complete-btn">
            <el-button type="success"
                       @click="submit"
                       :disabled="loading">
                开始使用
            </el-button>
        </div>
    </div>
</template>

<script>
import { perfectInformation } from '@/api/user';
import storage from '@/assets/js/storage';

export default {
	name: 'ViewComplete',
	data() {
		return {
			canvas: this.$refs.canvas,
			ctx: null,
			loading: true,
			text: '正在创建...',
			isSuccess: false,
			iconClass: 'icon-success_info',
			user: {},
		};
	},
	methods: {
		// 提交
		submit() {
			this.$router.push({ name: 'home' });
		},

		// 创建
		perfectInformationFn() {
			this.loading = true;
			this.text = '正在创建...';
			perfectInformation({
				userData: this.userData,
				groupData: this.groupData,
			})
				.then(resData => {
					this.iconClass = 'icon-success_info';
					this.text = '创建成功！';
					storage.set('token', resData.token);
					this.$store.dispatch('token', resData.token);
					this.$store.dispatch('user', resData.userInfo);
					this.$store.dispatch('group', resData.groupInfo);
					this.$store.dispatch('rooms', resData.rooms);
					this.user = resData.userInfo;
					this.loading = false;
				})
				.catch(error => {
					this.iconClass = 'icon-error';
					this.loading = false;
					this.text = error.message;
					this.$message({
						showClose: true,
						center: true,
						message: error.message,
						type: 'error',
					});
				});
		},

		// canvas
		canvasInit() {
			this.canvas = this.$refs.canvas;
			this.ctx = this.canvas.getContext('2d');
			const [radius, width, height] = [
				[0, 0.98],
				this.canvas.width,
				this.canvas.height,
			];
			this.step(radius, width, height);
		},

		draw(radius, width, height) {
			this.ctx.clearRect(0, 0, width, height);
			this.ctx.save();

			let opacity = 0;
			radius[0] <= 0.2 ? (opacity = 0.2) : (opacity = radius[0]);
			this.ctx.lineWidth = 0.0001;
			this.ctx.shadowColor = 'rgba(65,105,225, 0.3)';
			this.ctx.shadowBlur = 40;
			this.ctx.fillStyle = `rgba(64,158,255, ${opacity})`;

			this.ctx.beginPath();
			this.ctx.arc(80, 80, radius[0] * 50, 0, 2 * Math.PI);
			this.ctx.fill();
			this.ctx.stroke();

			this.ctx.beginPath();
			this.ctx.arc(80, 80, radius[1] * 50, 0, 2 * Math.PI);
			this.ctx.fill();
			this.ctx.stroke();

			this.ctx.restore();
		},

		step(radius, height, width) {
			let temp = [0, 0];
			for (let i = 0; i < radius.length; i++) {
				if (radius[i] <= 0.98) {
					radius[i] += 0.02;
					temp[i] = radius[i];
				} else if (radius[i] <= 1.98) {
					radius[i] += 0.02;
					temp[i] = 2 - radius[i];
				} else {
					radius[i] = 0;
				}
			}

			this.draw(temp, width, height);
			setTimeout(() => {
				this.step(radius, height, width);
			}, 35);
		},
	},

	props: {
		userData: {
			type: Object,
		},

		groupData: {
			type: Object,
		},
	},

	created() {
		this.perfectInformationFn();
	},

	mounted() {
		this.canvasInit();
	},
};
</script>

<style lang="scss" scoped>
.view-complete {
	height: 100%;
	position: relative;

	@include flex-center(column);
	justify-content: flex-start;

	&-content {
		width: 160px;
		height: 160px;
		margin-top: 60px;

		&-icon {
			display: block;
			width: 100%;
			height: 100%;
			background-size: 100% 100%;
			background-position: center center;
			background-repeat: no-repeat;
		}
	}

	&-text {
		user-select: none;
		margin-top: 20px;
		color: #909399;
	}

	&-user {
		margin-top: 20px;
		@include flex-center(column);

		span {
			user-select: none;
			color: #909399;
			margin-top: 15px;
			@include flex-center(column);
		}
		i {
			user-select: text;
			color: #606266;
			margin-top: 15px;
		}
	}

	&-btn {
		margin: 0;
		font-size: 16px;
		position: absolute;
		bottom: 70px;

		.el-button {
			width: 160px;
			font-size: 16px;
		}
	}
}
</style>
