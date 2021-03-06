<template>
<div class="rsqzvsbo">
	<div class="_panel about" v-if="meta">
		<div class="banner" :style="{ backgroundImage: `url(${ meta.bannerUrl })` }"></div>
		<div class="body">
			<h1 class="name" v-html="meta.name || host"></h1>
			<div class="desc" v-html="meta.description || $t('introMisskey')"></div>
			<div v-if="meta.disableRegistration && meta.disableInvitation" class="signup-disabled">
				<h1><fa :icon="faExclamationTriangle" /> {{ $t('signupDisabled') }}</h1>
				<p v-if="meta.disableInvitationReason" v-text="meta.disableInvitationReason"/>
			</div>
			<mk-button v-if="!(meta.disableRegistration && meta.disableInvitation)" @click="signup()" style="display: inline-block; margin-right: 16px;" primary>{{ $t('signup') }}</mk-button>
			<mk-button @click="signin()" style="display: inline-block;">{{ $t('login') }}</mk-button>
		</div>
	</div>
	<div class="_panel header" v-if="endpoint">
		<fa :icon="notesIcon"/>
		<span style="margin-left: 0.5em" v-text="notesHeader" />
	</div>
	<x-notes v-if="endpoint" :pagination="pagination"/>
</div>
</template>

<script lang="ts">
import Vue from 'vue';
import { toUnicode } from 'punycode';
import { faExclamationTriangle, faFireAlt, faComments, faGlobe } from '@fortawesome/free-solid-svg-icons'
import XSigninDialog from '../components/signin-dialog.vue';
import XSignupDialog from '../components/signup-dialog.vue';
import MkButton from '../components/ui/button.vue';
import XNotes from '../components/notes.vue';
import { host } from '../config';

export default Vue.extend({
	components: {
		MkButton,
		XNotes,
	},

	data() {
		return {
			host: toUnicode(host),
			faExclamationTriangle
		};
	},

	computed: {
		meta() {
			return this.$store.state.instance.meta;
		},
		endpoint() {
			return  !this.meta.disableFeatured ? 'notes/featured' :
							!this.meta.disableLocalTimeline ? 'notes/local-timeline' :
							!this.meta.disableGlobalTimeline ? 'notes/global-timeline' : null;
		},
		notesIcon() {
			switch (this.endpoint) {
				case 'notes/featured': return faFireAlt;
				case 'notes/local-timeline': return faComments;
				case 'notes/global-timeline': return faGlobe;
				default: return null;
			}
		},
		notesHeader() {
			return this.$t(this.endpoint === 'notes/featured' ? 'welcomeFeatured' : 'welcomeTimeline');
		},
		pagination() {
			if (this.endpoint === null) return null;
			return {
				endpoint: this.endpoint,
				limit: 10,
				noPaging: true,
			};
		}
	},

	created() {
		this.$root.api('stats').then(stats => {
			this.stats = stats;
		});
	},

	methods: {
		signin() {
			this.$root.new(XSigninDialog, {
				autoSet: true
			});
		},

		signup() {
			this.$root.new(XSignupDialog, {
				autoSet: true
			});
		}
	}
});
</script>

<style lang="scss" scoped>
.rsqzvsbo {
	> .about {
		overflow: hidden;
		margin-bottom: var(--margin);

		> .banner {
			height: 170px;
			background-size: cover;
			background-position: center center;
		}

		> .body {
			padding: 32px;

			@media (max-width: 500px) {
				padding: 16px;
			}

			> .name {
				margin: 0 0 0.5em 0;
			}

			> .signup-disabled {
				color: var(--infoWarnFg);
				background-color: var(--infoWarnBg);
				padding: 8px;
				margin: 8px 0;
				h1 {
					font-size: 1em;
					margin: 0 0 8px 0;
				}
				p {
					margin: 0 0 0 8px;
				}
			}
		}
	}

	> .header {
		padding: 16px;
	}
}
</style>
