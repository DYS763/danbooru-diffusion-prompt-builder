<!------------------------------------------------------------------------------
  - Danbooru Diffusion Prompt Builder
  - Copyright (C) 2022  Jabasukuriputo Wang
  -
  - This program is free software: you can redistribute it and/or modify
  - it under the terms of the GNU Affero General Public License as published by
  - the Free Software Foundation, either version 3 of the License, or
  - (at your option) any later version.
  -
  - This program is distributed in the hope that it will be useful,
  - but WITHOUT ANY WARRANTY; without even the implied warranty of
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
  - GNU Affero General Public License for more details.
  -
  - You should have received a copy of the GNU Affero General Public License
  - along with this program.  If not, see <https://www.gnu.org/licenses/>.
  -
  ----------------------------------------------------------------------------->

<script lang="ts" setup>
import { computed } from 'vue'
import { ElButton, ElCard, ElImage } from 'element-plus'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import {
    faCloudArrowDown,
    faImageSlash,
} from '@fortawesome/pro-regular-svg-icons'
import type { Hypernetwork } from '../types/data'
import ToggleableTag from './ToggleableTag.vue'

const props = withDefaults(
    defineProps<{
        data: Hypernetwork
        showCategory?: boolean
    }>(),
    {
        showCategory: false,
    }
)

const imageUrl = computed(() => {
    const hash = props.data.previewHash
    if (hash) {
        return `images/${hash.slice(0, 2)}/${hash}.webp`
    }
    return null
})

const downloadUrl = computed(() => {
    return props.data.payloadURL
})
</script>

<template>
    <ElCard :body-style="{ padding: '0px' }" class="box-card">
        <div v-if="imageUrl" :class="['card-image-container']">
            <ElImage :src="imageUrl" fit="cover" loading="lazy">
                <template #error>
                    <div class="image-slot">
                        <FontAwesomeIcon :icon="faImageSlash" size="lg" />
                    </div>
                </template>
            </ElImage>
        </div>

        <div class="imagecard-content">
            <div class="card-header flex-button-container">
                <div class="tag-header">
                    <code class="tag-name large">{{ data.prompt }}</code>
                </div>
                <div class="big-download-button">
                    <a
                        :href="downloadUrl"
                        target="_blank"
                        class="text-decoration-none">
                        <ElButton
                            type="warning"
                            color="#533F20"
                            round
                            class="download-btn">
                            <FontAwesomeIcon
                                :icon="faCloudArrowDown"
                                class="icon" />
                            下载模型
                        </ElButton>
                    </a>
                </div>
            </div>
            <div v-if="data.name" class="text name">{{ data.name }}</div>
            <div v-if="showCategory" class="text category">
                类别：{{ data.category.join('/') }}
            </div>
            <div v-if="data.author" class="text author">
                来源：{{ data.author }}
            </div>
            <p v-if="data.description" class="text description">
                {{ data.description }}
            </p>
            <div v-if="data.modelName" class="text meta">
                模型名：<code>{{ data.modelName }}</code> (<code>{{
                    data.modelHash
                }}</code
                >)
            </div>
            <div v-if="data.steps" class="text meta">
                训练步数：{{ data.steps }}
            </div>

            <div v-if="data.suggestPositive" class="tag-suggestion">
                <div>推荐正向标签：</div>
                <div class="tags">
                    <ToggleableTag
                        v-for="tag in data.suggestPositive"
                        :key="tag"
                        :tag="tag"
                        size="default"
                        direction="positive" />
                </div>
            </div>

            <div v-if="data.suggestNegative" class="tag-suggestion">
                <div>推荐反向标签</div>
                <div class="tags">
                    <ToggleableTag
                        v-for="tag in data.suggestNegative"
                        :key="tag"
                        :tag="tag"
                        size="default"
                        direction="negative" />
                </div>
            </div>
        </div>
    </ElCard>
</template>

<style lang="scss" scoped>
.tag-name {
    user-select: all;
    font-family: Consolas, 'Liberation Mono', Menlo, Courier, monospace;

    &.large {
        font-size: 12pt;
        font-weight: bold;
    }
}

.buttons {
    display: inline-block;

    & > * {
        margin-left: 0.6rem;
    }
}

.buttons-group {
    margin-left: auto;
    text-align: right;

    > div:first-child {
        margin-bottom: 0.75rem;
    }
}

.card-image-container {
    min-height: 256px;
    aspect-ratio: 1 / 1;
    transition: 0.5s all;

    img {
        width: 100%;
        height: 100%;
        object-fit: cover;
    }
}

.tag-header {
    height: 32px;

    .tag-name {
        line-height: 32px;
    }

    margin-right: 0.5rem;
}

.card-image {
    width: 100%;
    height: 100%;
    object-fit: contain;
}

.card-header {
    margin-bottom: 1.5rem;
    row-gap: 0.75rem;
}

.flex-button-container {
    display: flex;
    flex-direction: row;
    vertical-align: middle;
    justify-content: space-between;
    flex-wrap: wrap;
}

.alias-tag {
    row-gap: 0.5rem;
}

.imagecard-content {
    padding: var(--el-card-padding);
}

.text {
    margin-bottom: 0.5rem;
}

.name {
    margin-bottom: 1rem;
    font-size: 14pt;
    font-weight: 400;
}

.description {
    margin-bottom: 1rem;
    word-wrap: break-word;
}

.tags {
    margin-top: 0.75rem;
    margin-bottom: 0.75rem;
    line-height: 2rem;

    > * {
        margin-right: 0.5rem;
    }
}

.tag-suggestion {
    margin-top: 1rem;
    margin-bottom: 1rem;
}

.download-btn {
    .icon {
        margin-right: 0.5rem;
    }
}

.text-decoration-none {
    text-decoration: none;
}

.big-download-button {
    display: inline-block;
}

.image-slot {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 100%;
    background: var(--el-fill-color-light);
    color: var(--el-text-color-secondary);
    font-size: 30px;
}
</style>
