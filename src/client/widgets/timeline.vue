<template>
<div class="mkw-timeline" :style="`flex-basis: calc(${basis}% - var(--margin)); height: ${previewHeight}px;`">
	<mk-container :show-header="!props.compact" class="container">
		<template #header>
			<button @click="choose" class="_button">
				<fa :icon="getIconOfTimeline(props.src)"/>
				<span style="margin-left: 8px;">{{ timelineTitle }}</span>
				<fa :icon="menuOpened ? faAngleUp : faAngleDown" style="margin-left: 8px;"/>
			</button>
		</template>

		<div>
			<x-timeline :key="props.src === 'list' ? `list:${props.list.id}` : props.src === 'antenna' ? `antenna:${props.antenna.id}` : props.src" :src="props.src" :list="props.list" :antenna="props.antenna"/>
		</div>
	</mk-container>
</div>
</template>

<script lang="ts">
import { faAngleDown, faAngleUp, faHome, faShareAlt, faGlobe, faListUl, faSatellite, faCat, faAt, faProjectDiagram } from '@fortawesome/free-solid-svg-icons';
import { faComments, faEnvelope, faCommentAlt } from '@fortawesome/free-regular-svg-icons';
import { getIconOfTimeline } from '../scripts/get-icon-of-timeline';
import MkContainer from '../components/ui/container.vue';
import XTimeline from '../components/timeline.vue';
import define from './define';

const basisSteps = [25, 50, 75, 100]
const previewHeights = [200, 300, 400, 500]

export default define({
	name: 'timeline',
	props: () => ({
		src: 'home',
		list: null,
		compact: false,
		basisStep: 0
	})
}).extend({
	
	components: {
		MkContainer,
		XTimeline,
	},

	data() {
		return {
			menuOpened: false,
			faAngleDown, faAngleUp, faHome, faShareAlt, faGlobe, faComments, faListUl, faSatellite, faCat, faAt, faEnvelope, faProjectDiagram, faCommentAlt
		};
	},

	computed: {
		basis(): number {
			return basisSteps[this.props.basisStep] || 25
		},

		previewHeight(): number {
			return previewHeights[this.props.basisStep] || 200
		},

		meta() {
			return this.$store.state.instance.meta;
		},

		timelineTitle() {
			return 	this.props.src === 'list' ?  this.props.list.name : 
							this.props.src === 'antenna' ? this.props.antenna.name : 
							this.props.src === 'mentions' ? this.$t('mentions') :
							this.props.src === 'direct' ? this.$t('directNotes') :
							this.$t('_timelines.' + this.props.src);
		}
	},

	methods: {
		getIconOfTimeline,
		func() {
			if (this.props.basisStep === basisSteps.length - 1) {
				this.props.basisStep = 0
				this.props.compact = !this.props.compact;
			} else {
				this.props.basisStep += 1
			}

			this.save();
		},

		async choose(ev) {
			if (this.meta == null) return;
			this.menuOpened = true;
			const [antennas, lists] = await Promise.all([
				this.$root.api('antennas/list'),
				this.$root.api('users/lists/list')
			]);
			const antennaItems = antennas.map(antenna => ({
				text: antenna.name,
				icon: faSatellite,
				action: () => {
					this.props.antenna = antenna;
					this.setSrc('antenna');
				}
			}));
			const listItems = lists.map(list => ({
				text: list.name,
				icon: faListUl,
				action: () => {
					this.props.list = list;
					this.setSrc('list');
				}
			}));
			this.$root.menu({
				items: [{
					text: this.$t('_timelines.home'),
					icon: faHome,
					action: () => { this.setSrc('home') }
				}, this.meta.disableLocalTimeline && !this.$store.state.i.isModerator && !this.$store.state.i.isAdmin ? undefined : {
					text: this.$t('_timelines.local'),
					icon: faComments,
					action: () => { this.setSrc('local') }
				}, this.meta.disableLocalTimeline && !this.$store.state.i.isModerator && !this.$store.state.i.isAdmin ? undefined : {
					text: this.$t('_timelines.social'),
					icon: faShareAlt,
					action: () => { this.setSrc('social') }
				}, this.meta.disableGlobalTimeline && !this.$store.state.i.isModerator && !this.$store.state.i.isAdmin ? undefined : {
					text: this.$t('_timelines.global'),
					icon: faGlobe,
					action: () => { this.setSrc('global') }
				}, this.meta.disableCatTimeline && !this.$store.state.i.isModerator && !this.$store.state.i.isAdmin ? undefined : {
					text: this.$t('_timelines.cat'),
					icon: faCat,
					action: () => { this.setSrc('cat') }
				}, {
					text: this.$t('_timelines.remoteFollowing'),
					icon: faProjectDiagram,
					action: () => { this.setSrc('remoteFollowing') }
				}, {
					text: this.$t('_timelines.followers'),
					icon: faCommentAlt,
					action: () => { this.setSrc('followers') }
				}, antennaItems.length > 0 ? null : undefined, ...antennaItems, listItems.length > 0 ? null : undefined, ...listItems, null, {
					text: this.$t('mentions'),
					icon: faAt,
					action: () => { this.setSrc('mentions') }
				}, {
					text: this.$t('directNotes'),
					icon: faEnvelope,
					action: () => { this.setSrc('direct') }
				}],
				fixed: true,
				noCenter: true,
				source: ev.currentTarget || ev.target
			}).then(() => {
				this.menuOpened = false;
			});
		},

		setSrc(src) {
			this.props.src = src;
			this.save();
		},
	}
});
</script>

<style lang="scss">
.mkw-timeline {
	flex-grow: 1;
	flex-shrink: 0;
	min-height: 0; // https://www.gwtcenter.com/min-height-required-on-firefox-flexbox

	.container {
		display: flex;
		flex-direction: column;
		height: 100%;

		> div {
			overflow: auto;
			flex-grow: 1;
		}
	}
}
</style>
