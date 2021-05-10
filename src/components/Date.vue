<template>
	<div class="date">
		<div class="date__title">
			Left until the draw
		</div>
		<div class="date__time">
			3 h
		</div>
	</div>
</template>

<script>

export default {
  name: 'Date',
}

</script>

<style lang="scss" scoped>

.date {
	background-color: #F3F3F3;
	padding: 28px 68px;
	font-weight: 600;
	border-radius: 24px;
	margin-bottom: 49px;

	&__title {
		font-size: 12px;
		line-height: 24px;
		margin-bottom: 8px;
	}

	&__time {
		font-size: 56px;
		line-height: 80px;
	}
}

</style>