<script>
import Tag from "./tag";
import { mapState } from 'vuex';
import { getAngle, makeMouseFirst } from "../utils";

export default {
    components: { Tag },
    props: {
        'data': {
            type: Object,
            default: function () {
                return { start: {x:0, y:0}, end: {x:0, y:0}};
            }
        }
    },
	directives: {
		tag: {
			bind(el, binding) {
				const rawObj = binding.value;
				$(el).attr(rawObj.tag, JSON.stringify(rawObj.data));
			},
			componentUpdated(el, binding) {
				const rawObj = binding.value;
				$(el).attr(rawObj.tag, JSON.stringify(rawObj.data));
			}
		}
    },
    computed: mapState({
        pathD: function () {
            const start = this.data.start;
            const end = makeMouseFirst(this.data);
            return ['M', start.x, start.y, 'L', end.x, end.y].join(' ');
        },
        arrowD: function () {
            const data = this.data.end;
            const x = data.x;
            const y = data.y;
            return ['M', x, y, 'L', x - 14, y - 6, 'L', x - 14, y + 6, 'z'].join(' ');
        },
        id: function () {
            return [this.data.start.id, this.data.end.id].join('_');
        },
        rotate: function() {
            const data = this.data;
            const configure = [getAngle(data), data.end.x, data.end.y].join();
            return ['rotate(', configure,')'].join('');
        },
        pathName: state => state.tool.path.name,
        lineTag: state => state.market.lineTag
    })
};
</script>

<template>
    <g :name="pathName" :id="id">
        <path class="link" :d="pathD" v-tag="{tag: lineTag, data}"></path>
        <path class="arrow" :d="arrowD" :transform="rotate"></path>
        <Tag data="Transition"></Tag>
    </g>
</template>

<style scoped lang="less">
.link {
    cursor: pointer;
    opacity: 0.8;
    stroke: #333333;
    stroke-width: 3;
}

.arrow {
    fill: #333333;
}
</style>