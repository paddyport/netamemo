<template>
<div class="content">
	<div class="head"><div>{{ viewTxtHead }}</div></div>
	<div class="body"><div>{{ viewTxtBody }}</div></div>
</div>
</template>

<script>
export default {
	name: "ViewTextContent",
	props: {
        viewTxtHead: String,
        viewTxtBody: String,
    },
}
</script>