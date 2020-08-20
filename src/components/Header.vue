<template>
	<div class="header-text">
		<span class="front-text">Black</span> 
		<span>Jack</span>
	</div>
</template>

<script>
export default {
	name: 'Header'
}
</script>

<style>

.header-text {
	position: fixed;
	top: 0;
	left: 0;
	font-family: 'Georgia';
	font-size: 35px;
	text-align: center;
	letter-spacing: 5px;
	padding: 20px;
	width: 100%;
	background: radial-gradient(rgb(209, 207, 207), red);
	z-index: 10;
}

.header-text span {
	display: inline-block;
} 

.front-text {
	margin-right: 17px;
}
</style>