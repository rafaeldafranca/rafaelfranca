<template>
    <div class="image-view">
        <!-- Image -->
        <img ref="img"
             v-show="isLoaded()"
             :src="props.src"
             :alt="props.alt"
             :class="classList"
             @load="_onLoad"
             @error="_onError">

        <!-- Loading Spinner -->
        <div v-if="props.spinnerEnabled"
             class="spinner"
             v-show="!isLoaded()">

            <!-- Base 64 Spinner -->
            <img alt="loading..." class="preloader-gif" src="/images/pictures/favicon-32x32.png" />
        </div>
    </div>
</template>

<script setup>
import {computed, onMounted, ref, watch} from "vue"
import {useConstants} from "../../composables/constants.js"

/**
 * @property {String} src
 * @property {String} alt
 * @property {Boolean} [ignoreOnImageCount=false]
 * @property {Boolean} [spinnerEnabled=false]
 */
const props = defineProps({
    src: String,
    alt: String,
    ignoreOnImageCount: Boolean,
    spinnerEnabled: Boolean
})

const constants = useConstants()

const emit = defineEmits(['loading', 'loaded', 'error', 'completed'])
const img = ref(null)
const loadStatus = ref(null)

/**
 * @type {ComputedRef<String>}
 */
const classList = computed(() => {
    return constants.HTML_CLASSES.imageViewImage + (props.ignoreOnImageCount ? ` ${constants.HTML_CLASSES.imageViewImageIgnoredOnCount}` : '')
})

/**
 * @private
 */
onMounted(() => {
    _onSourceChanged()
})

/**
 * @private
 */
watch(() => props.src, () => {
    _onSourceChanged()
})

/**
 * @return {boolean}
 */
const isLoaded = () => {
    return loadStatus.value === constants.LoadStatus.LOADED || loadStatus.value === constants.LoadStatus.ERROR
}

/**
 * @private
 */
const _onLoad = () => {
    _setLoadStatus(constants.LoadStatus.LOADED)
}

/**
 * @private
 */
const _onError = () => {
    _setLoadStatus(constants.LoadStatus.ERROR)
}

/**
 * @param {constants.LoadStatus} status
 * @private
 */
const _setLoadStatus = (status) => {
    loadStatus.value = status
    img.value.setAttribute('loadStatus', status)
    switch(status) {
        case constants.LoadStatus.LOADING:
            emit('loading')
            break

        case constants.LoadStatus.LOADED:
            emit('loaded')
            emit('completed')
            break

        case constants.LoadStatus.ERROR:
            emit('error')
            emit('completed')
            break
    }
}

/**
 * @private
 */
const _onSourceChanged = () => {
    _setLoadStatus(typeof props.src !== 'string' ? constants.LoadStatus.ERROR : constants.LoadStatus.LOADING)
}

defineExpose({
    isLoaded
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.image-view {
    display: inline-block;
    overflow: hidden;
}

.image-view-img {
    width: 100%;
    height: 100%;
    vertical-align: top;
}

.spinner {
    display: flex;
    justify-content: center;
    align-items: center;

    width: 100%;
    height: 100%;
    background-color: rgba($dark, 0.05);
}

.preloader-gif {
    @include generate-dynamic-styles-with-hash((
        xxxl:   (max-width: 200px,  max-height:200px),
        md:     (max-width: 130px,  max-height:130px),
        sm:     (max-width: 90px,   max-height:90px),
    ));

    margin: 0 auto;
    width: 50%;
    height: 50%;
}
</style>