<script>
    import abstractBaseType from '../abstracts/_abstractInputType';

    export default {
        name: 'setting-questiontheme',
        extends: abstractBaseType,
        /*
        Abstract base provides props: 
         - elId
         - elName
         - elLabel
         - elHelp
         - currentValue
         - elOptions
         - readonly
         - debug
        */
        /*
        Abstract base provides data: 
         - triggerShowHelp
        */
        computed: {
            /*
            Abstract base provides computed values: 
             - curValue
             - getClasses
             - showHelp
             - hasPrefix
             - hasSuffix
            */
            curValue: {
                get() { return this.currentValue },
                set(newValue) { 
                    this.$emit('change', newValue);
                    this.$store.commit('setStoredEvent', { target: 'GeneralSettings', method: 'toggleTimedLoading', content: true, chain: 'AdvancedSettings' });
                    Promise.all([
                        this.$store.dispatch('getQuestionGeneralSettings'),
                        this.$store.dispatch('getQuestionAdvancedSettings')
                    ]).then((e)=>{
                        this.$store.commit('setStoredEvent', { target: 'GeneralSettings', method: 'toggleTimedLoading', content: false, chain: 'AdvancedSettings' });
                    });
                },
            },
        },
        methods: {
            simpleValue(value) {
                if(value == []) {
                    return null;
                }
                return value;
            },
        }
    };
</script>

<template>
    <div class="form-row">
        <i 
            class="fa fa-question pull-right" 
            @click="triggerShowHelp=!triggerShowHelp" 
            v-if="(elHelp.length>0) && !readonly" 
            :aria-expanded="!triggerShowHelp" 
            :aria-controls="'help-'+(elName || elId)"
        />
        <label class="form-label" :for="elId"> {{elLabel}} </label>
        <select 
            v-model="curValue"
            :class="getClasses" 
            :name="elName || elId" 
            :id="elId" 
            :disabled="readonly"
        >
            <option 
                v-for="(optionObject, i) in elOptions.options"
                :key="i"
                :value="simpleValue(optionObject.value)"
            >
                {{optionObject.text}}
            </option>
        </select>
        <transition name="fade">
            <div 
                class="question-option-help well"
                :id="'help-'+(elName || elId)"
                v-show="showHelp"
                v-html="elHelp"
            />
        </transition>
    </div>
</template>