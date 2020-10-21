<template>
<div class="content">
	<div class="head"><div>{{ viewDcmHead }}</div></div>
	<div class="body"><div>{{ viewDcmBody }}</div></div>
</div>
</template>

<script>
export default {
// CH Component
	name: "ViewDocumentContent",
	props: {
        viewDcmHead: String,
        viewDcmBody: String,
    },
}
</script>