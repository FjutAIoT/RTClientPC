<template>
    <el-input class="app-header-search"
              :placeholder="placeholder"
              suffix-icon="el-icon-search"
              v-model="searchInput">
    </el-input>
</template>

<script>
export default {
	name: 'AppHeaderSearch',
	data() {
		return {
			searchInput: '',
		};
	},
	props: {
		placeholder: {
			type: String,
			default: 'Search...',
		},
	},
};
</script>

<style lang="scss">
.header-content {
	.el-input > .el-input__inner {
		border-radius: 40px;
	}
}
</style>
