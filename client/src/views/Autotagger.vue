<template>
<div>

    <q-stepper 
        v-model='step' 
        header-nav 
        color='primary' 
        animated 
        alternative-labels
        flat 
        class='bg-dark-page'
        v-if='!$1t.settings.autoTaggerSinglePage'>

        <!-- Platforms -->
        <q-step 
            :name='0' 
            title='Select Platforms' 
            :done='step > 0 && $1t.config.platforms.length > 0'
            icon='mdi-web'
            :error='step > 0 && $1t.config.platforms.length == 0'
            class='text-center step'>

            <div class='text-h5 text-grey-4'>Select platforms</div>
            <div class='text-subtitle1 q-mt-xs text-grey-6'>Use the checkbox to enable/disable, drag and drop to reorder fallback</div>
            <AutotaggerPlatforms class='q-mb-xl'></AutotaggerPlatforms>
        </q-step>

        <!-- Tags -->
        <q-step
            :name='1'
            title='Input & Tags'
            :done='canStart && step > 1'
            icon='mdi-label-multiple'
            :error='!canStart && step > 1'
            class='text-center step'>

            <AutotaggerTags class='q-px-xl q-mx-xl q-mb-xl'></AutotaggerTags>
        </q-step>

        <!-- Platform Specific -->
        <q-step
            :name='2'
            title='Platform Specific Settings'
            :done='step > 2'
            icon='mdi-tune'
            class='text-center step'>

            <AutotaggerPlatformSpecific class='q-mb-xl'></AutotaggerPlatformSpecific>
        </q-step>

        <!-- Advanced -->
        <q-step
            :name='3'
            title='Advanced'
            :done='step > 3'
            icon='mdi-cog'
            class='text-center step'>

            <div class='text-h5 q-my-s text-grey-4'>Advanced</div>
            <AutotaggerAdvanced class='q-mb-xl'></AutotaggerAdvanced>
        </q-step>

    </q-stepper>

    <!-- Stepper bar -->
    <div class='at-stepper-bar row justify-center content-center' v-if='!$1t.settings.autoTaggerSinglePage'>
        <div>
            <q-btn push color='primary' class='text-black' @click='step += 1' v-if='step < 3'>
                Next
            </q-btn>
        </div>
    </div>

    <!-- Single page -->
    <div v-if='$1t.settings.autoTaggerSinglePage' class='text-center'>
        <div class='row q-mx-xl'>
            <div class='col q-px-xl'>
                <AutotaggerTags class='q-mt-md'></AutotaggerTags>
                <AutotaggerAdvanced class='q-mt-md'></AutotaggerAdvanced>
            </div>
            <div class='col q-px-xl'>
                <div class='text-h5 q-mt-md text-grey-4'>Select platforms</div>
                <div class='text-subtitle1 q-mt-xs text-grey-6'>Use the checkbox to enable/disable, drag and drop to reorder fallback</div>
                <AutotaggerPlatforms dense></AutotaggerPlatforms>
                <AutotaggerPlatformSpecific></AutotaggerPlatformSpecific>
            </div>
        </div>
        
    </div>

    <!-- Start FAB -->
    <q-page-sticky position='bottom-right' :offset='[36, 24]'>
        <q-btn 
            fab 
            push
            icon='mdi-play' 
            color='primary'
            :disable='!canStart'
            @click='startTagging'
        >
            <q-tooltip anchor="top middle" self="bottom middle" :offset="[10, 10]">            
                <span class='text-weight-bold'>START</span>
            </q-tooltip>
        </q-btn>
    </q-page-sticky>

</div>
</template>

<script>
import AutotaggerPlatforms from '../components/AutotaggerPlatforms';
import AutotaggerTags from '../components/AutotaggerTags';
import AutotaggerPlatformSpecific from '../components/AutotaggerPlatformSpecific';
import AutotaggerAdvanced from '../components/AutotaggerAdvanced';

export default {
    name: 'Autotagger',
    components: {AutotaggerPlatforms, AutotaggerTags, AutotaggerPlatformSpecific, AutotaggerAdvanced},
    data() {
        return {
            step: 0
        }
    },
    methods: {
        startTagging() {
            this.$1t.saveSettings();
            this.$1t.config.type = 'autoTagger';

            //Tag playlist rather than folder
            let playlist = null;
            if (this.$1t.autoTaggerPlaylist && this.$1t.autoTaggerPlaylist.data)
                playlist = this.$1t.autoTaggerPlaylist;

            this.$1t.send('startTagging', {
                config: this.$1t.config,
                playlist
            });
            this.$router.push('/autotagger/status');
        }
    },
    computed: {
        //If tagging can be started
        canStart() {
            //Path or playlist & atleast 1 platform
            return (this.$1t.config.path || (this.$1t.autoTaggerPlaylist && this.$1t.autoTaggerPlaylist.data)) && 
                this.$1t.config.platforms.length > 0;
        }
    }
};
</script>

<style>
.step {
    min-height: calc(100vh - 164px);
    max-height: calc(100vh - 164px);
    background: #1a1c1b;
}
.q-stepper__step-inner {
    background: #1a1c1b;
}

.input {
    max-width: 50vw;
    margin: auto;
    margin-top: 8px;
    padding-left: 16px;
    padding-right: 16px;
}
.select {
    max-width: 50vw;
    margin: auto;
    margin-top: 8px;
    padding-left: 16px;
    padding-right: 16px;
}
.slider {
    max-width: 34vw;
}

.at-stepper-bar {
    width: 100%;
    position: absolute;
    height: 64px;
    bottom: 0%;
    background-color: var(--q-color-accent);
}
</style>