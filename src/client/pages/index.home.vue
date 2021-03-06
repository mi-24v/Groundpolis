<template>
<div class="mk-home" v-hotkey.global="keymap">
	<template v-if="showTitle">
		<portal to="header" v-if="!$store.state.deviceUser.hideTimelineColumn">
			<button v-if="isMobile" @click="choose" class="_button _kjvfvyph_">
				<i><fa v-if="$store.state.i.hasUnreadAntenna" :icon="faCircle"/></i>
				<fa :icon="getIconOfTimeline(src)"/>
				<span style="margin-left: 8px;">{{ timelineTitle }}</span>
				<fa :icon="menuOpened ? faAngleUp : faAngleDown" style="margin-left: 8px;"/>
			</button>
			<div class="tabs" v-else>
				<button class="_button tab" v-for="(item, i) in tabItems" :key="i" :class="{ active: item.src === src }" v-tooltip="item.text" @click="item.action">
					<fa :icon="item.icon" />
					<span v-if="item.src === src" v-text="item.text" style="margin-left: 8px" />
				</button>
				<div class="_button tab active" v-if="!tabItems.map(i => i.src).includes(src)">
					<fa :icon="getIconOfTimeline(src)"/>
					<span style="margin-left: 8px;">{{ timelineTitle }}</span>
				</div>
				<button class="_button tab _kjvfvyph_" @click="choose">
					<fa :icon="faEllipsisH" />
				</button>
			</div>
		</portal>
		<template v-else>
			<portal to="icon"><fa :icon="faHome"/></portal>
			<portal to="title">{{ $t('timeline') }}</portal>
		</template>
	</template>

	<div class="new" v-if="queue > 0" :style="{ width: width + 'px' }"><button class="_buttonPrimary" @click="top()">{{ $t('newNoteRecived') }}</button></div>

	<x-tutorial class="tutorial" v-if="$store.state.settings.tutorial != -1"/>
	<section class="_card announcements" v-else-if="$store.getters.isSignedIn && announcements.length > 0">
		<div class="_title">{{ currentAnnouncement.title }}</div>
		<div class="_content">
			<mfm :text="currentAnnouncement.text"/>
			<img v-if="currentAnnouncement.imageUrl" :src="currentAnnouncement.imageUrl" alt=""/>
		</div>
		<div class="_footer navigation">
			<button class="arrow" @click="currentAnnouncementIndex--" :disabled="currentAnnouncementIndex == 0">
				<fa :icon="faChevronLeft"/>
			</button>
			<span>{{ currentAnnouncementIndex + 1 }} / {{ announcements.length }}</span>
			<button class="arrow" @click="currentAnnouncementIndex++" :disabled="currentAnnouncementIndex == announcements.length - 1">
				<fa :icon="faChevronRight"/>
			</button>
			<mk-button class="ok" @click="read(currentAnnouncement)" primary><fa :icon="faCheck"/> {{ $t('gotIt') }}</mk-button>
		</div>
	</section>
	
	<x-post-form class="post-form _panel" fixed v-if="$store.state.device.showFixedPostForm"/>
	<x-timeline ref="tl" :key="src === 'list' ? `list:${list.id}` : src === 'antenna' ? `antenna:${antenna.id}` : src" :src="src" :list="list" :antenna="antenna" :sound="true" @before="before()" @after="after()" @queue="queueUpdated"/>
</div>
</template>

<script lang="ts">
import Vue from 'vue';
import { faAngleDown, faAngleUp, faHome, faListUl, faBroadcastTower, faCircle, faChevronLeft, faChevronRight, faCheck, faEllipsisH } from '@fortawesome/free-solid-svg-icons';
import Progress from '../scripts/loading';
import { getIconOfTimeline } from '../scripts/get-icon-of-timeline';
import XTimeline from '../components/timeline.vue';
import MkButton from '../components/ui/button.vue';
import XPostForm from '../components/post-form.vue';

export default Vue.extend({
	metaInfo() {
		return {
			title: this.$t('timeline') as string
		};
	},

	components: {
		XTimeline,
		MkButton,
		XPostForm,
		XTutorial: () => import('./index.home.tutorial.vue').then(m => m.default),
	},

	props: {
		showTitle: {
			type: Boolean,
			required: true
		}
	},

	data() {
		return {
			src: 'home',
			list: null,
			antenna: null,
			menuOpened: false,
			announcements: [] as any[],
			queue: 0,
			width: 0,
			isMobile: window.innerWidth < 650,
			currentAnnouncementIndex: 0,
			faAngleDown, faAngleUp, faHome, faCircle, faChevronLeft, faChevronRight, faCheck, faEllipsisH
		};
	},

	computed: {
		keymap(): any {
			return {
				't': this.focus
			};
		},
		currentAnnouncement() {
			if (this.announcements.length > 0) {
				if (this.currentAnnouncementIndex < 0)
					this.currentAnnouncementIndex = 0;
				if (this.currentAnnouncementIndex >= this.announcements.length)
					this.currentAnnouncementIndex = this.announcements.length - 1;
				
				return this.announcements[this.currentAnnouncementIndex];
			}
			return null;
		},

		tabItems() {
			return [
				this.genItem('home'),
				this.ifLTL(this.genItem('local')),
				this.ifLTL(this.genItem('social')),
				this.ifGTL(this.genItem('global')),
				this.ifCTL(this.genItem('cat')),
				this.genItem('remoteFollowing'),
				this.genItem('followers'),
			].filter(it => it !== undefined);
		},

		meta() {
			return this.$store.state.instance.meta;
		},

		timelineTitle() {
			return 	this.src === 'list' ?  this.list.name : 
							this.src === 'antenna' ? this.antenna.name : 
							this.src === 'mentions' ? this.$t('mentions') :
							this.src === 'direct' ? this.$t('directNotes') :
							this.$t('_timelines.' + this.src);
		},
	},

	watch: {
		src() {
			this.showNav = false;
			this.saveSrc();
		},
		list(x) {
			this.showNav = false;
			this.saveSrc();
			if (x != null) this.antenna = null;
		},
		antenna(x) {
			this.showNav = false;
			this.saveSrc();
			if (x != null) this.list = null;
		},
	},

	created() {
		this.src = this.$store.state.deviceUser.tl.src;
		if (this.src === 'list') {
			this.list = this.$store.state.deviceUser.tl.arg;
		} else if (this.src === 'antenna') {
			this.antenna = this.$store.state.deviceUser.tl.arg;
		}
	},

	activated() {
		this.$root.api('announcements', { limit: 100, withUnreads: true }).then((a: any) => {
			this.announcements = a
		});
	},

	mounted() {
		this.width = this.$el.offsetWidth;

		window.addEventListener('resize', () => {
			this.isMobile = window.innerWidth < 650;
		}, { passive: true });
	},

	methods: {
		getIconOfTimeline, 
		before() {
			Progress.start();
		},

		after() {
			Progress.done();
		},

		queueUpdated(q) {
			if (this.$el.offsetWidth !== 0) this.width = this.$el.offsetWidth;
			this.queue = q;
		},

		top() {
			window.scroll({ top: 0, behavior: 'instant' });
		},

		genItem(src: string, name?: string) {
			return {
				text: name || this.$t('_timelines.' + src),
				icon: getIconOfTimeline(src),
				src,
				action: () => { this.setSrc(src); }
			};
		},

		ifLTL<T>(item: T) : (T | undefined) {
			return this.meta.disableLocalTimeline && !this.$store.state.i.isModerator && !this.$store.state.i.isAdmin ? undefined : item;
		},

		ifGTL<T>(item: T) : (T | undefined) {
			return this.meta.disabledGlobalTimeline && !this.$store.state.i.isModerator && !this.$store.state.i.isAdmin ? undefined : item;
		},

		ifCTL<T>(item: T) : (T | undefined) {
			return this.meta.disableCatTimeline && !this.$store.state.i.isModerator && !this.$store.state.i.isAdmin ? undefined : item;
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
				icon: faBroadcastTower,
				indicate: antenna.hasUnreadNote,
				action: () => {
					this.antenna = antenna;
					this.setSrc('antenna');
				}
			}));
			const listItems = lists.map(list => ({
				text: list.name,
				icon: faListUl,
				action: () => {
					this.list = list;
					this.setSrc('list');
				}
			}));
			
			this.$root.menu({
				items: [
					...(this.isMobile ? this.tabItems : [ undefined ]),
					antennaItems.length > 0 ? null : undefined,
					...antennaItems,
					listItems.length > 0 ? null : undefined,
					...listItems,
					null,
					this.genItem('mentions', this.$t('mentions').toString()),
					this.genItem('direct', this.$t('directNotes').toString()),
				],
				fixed: true,
				noCenter: true,
				source: ev.currentTarget || ev.target
			}).then(() => {
				this.menuOpened = false;
			});
		},

		setSrc(src: string) {
			this.src = src;
		},

		saveSrc() {
			this.$store.commit('deviceUser/setTl', {
				src: this.src,
				arg: this.src == 'list' ? this.list : this.antenna
			});
		},

		focus() {
			(this.$refs.tl as any).focus();
		},

		read(announcement: any) {
			this.announcements = this.announcements.filter(a => a != announcement)
			this.$root.api('i/read-announcement', { announcementId: announcement.id });
		},
	}
});
</script>

<style lang="scss" scoped>

.announcements {
	margin-bottom: var(--margin);

	> .navigation {
		display: flex;
		flex-direction: row;
		align-items: baseline;
		font-size: 18px;

		> .arrow {
			color: var(--fg);
			background: none;
			border: none;
			font-size: inherit;

			&:disabled {
				opacity: 0.6;
			}
		}

		> .ok {
			margin-left: auto;
		}
	}
}

.post-form {
	margin-bottom: var(--margin);
}

.tabs {
	display: flex;
	align-items: center;
	height: 100%;
	> .tab {
		display: block;
		min-width: 64px;
		padding: 0 16px;
		height: 100%;
		border-left: 1px solid var(--divider);

		&:last-child {
			border-right: 1px solid var(--divider);
		}

		&.active {
			color: var(--accent);
			background: var(--panel);
		}
	}
}

.mk-home {
	> .new {
		position: fixed;
		z-index: 1000;

		> button {
			display: block;
			margin: 0 auto;
			padding: 8px 16px;
			border-radius: 32px;
		}
	}

	> .tutorial {
		margin-bottom: var(--margin);
	}

	> .post-form {
		position: relative;
		margin-bottom: var(--margin);
	}
}

._kjvfvyph_ {
	position: relative;
	height: 100%;
	padding: 0 16px;
	font-weight: bold;

	> i {
		position: absolute;
		top: 16px;
		right: 8px;
		color: var(--indicator);
		font-size: 12px;
		animation: blink 1s infinite;
	}
}
</style>
