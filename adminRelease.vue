<template>
    <div class="admin-release-container">
        <h1 class="admin-release-title">管理员发布商品</h1>
        <release></release>
    </div>
</template>

<script>
import Release from '../page/release.vue';

export default {
    components: {
        Release
    }
};
</script>

<style scoped>
.admin-release-container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    border: 1px solid #ccc;
    border-radius: 5px;
}

.admin-release-title {
    text-align: center;
    margin-bottom: 20px;
}
</style>