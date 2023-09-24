<template>
    <div class="activity-spinner" :class="{'d-none': !isShowing}">
        <div class="activity-spinner-content text-center">
            <!-- Base 64 Spinner -->
            <img alt="loading..." class="preloader-gif" src="/images/pictures/favicon-32x32.png" />

            <!-- Message -->
            <h5 class="text-light-5">{{ message }}</h5>
        </div>
    </div>
</template>

<script setup>
import {ref} from "vue"

const message = ref('')
const isShowing = ref(false)

/**
 * @public
 * @param {String} _message
 */
const show = (_message) => {
    message.value = _message
    isShowing.value = true
}

/**
 * @public
 */
const hide = () => {
    isShowing.value = false
}

defineExpose({
    show,
    hide
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.activity-spinner {
    position: fixed;

    display: flex;
    justify-content: center;
    align-items: center;

    z-index: 999;
    background-color: rgba(0, 0, 0, 0.90);
    width: 100vw;
    height: 100vh;
}

.preloader-gif {
    @include generate-dynamic-styles-with-hash((
        xxxl:   (max-width: 200px,  max-height:200px),
        md:     (max-width: 150px,  max-height:150px),
        sm:     (max-width: 120px,  max-height:120px),
    ));
}
</style>