<template>

    <!-- 변수 삽입 -->
    <div class="layer_popup preview" v-if="visible">
        <h3>변수 삽입</h3>
        <p class="desc">다음의 변수가 AOSBOX AI Plus의 표준 폴더 경로(장소)로 식별됩니다. 변수를 복사하려면 변수를 선택하고 "변수를 삽입"를 클릭합니다. </p>
        <table class="board_list">
            <caption>{{title}}</caption>
            <colgroup>
                <col style="width:50px"/>
                <col style="width:150px"/>
                <col style="width:280px"/>
                <col style="width:280px"/>
            </colgroup>
            <thead>
            <tr>
                <th scope="col" colspan="2">변수</th>
                <th scope="col">Mac OS X</th>
                <th scope="col">Windows</th>
            </tr>
            </thead>
            <tbody>
            <tr v-for="item in variableList">
                <td><input type="radio" v-model="vcheck" :value="item.bv_variable"/></td>
                <td class="left">{{item.bv_variable}}</td>
                <td>{{item.bv_macpath}}{{filepath}}</td>
                <td>{{item.bv_windowspath}}</td>
            </tr>
            </tbody>
        </table>
        <ul class="desc_list" style="margin-top:15px;">
            <li>전체 9건 중 1에서 9을 표시중</li>
        </ul>
        <div class="btn_set">
            <button v-if="insert_div == 'file'" type="button" class="btn_basic submit" @click="sendVariable">변수를 삽입
            </button>
            <button v-else type="button" class="btn_basic submit" @click="sendFolderVariable">변수를 삽입</button>
            <button type="button" class="btn_basic cancel" @click="$emit('update:visible', false)">닫기</button>
        </div>
        <button class="btn_close" @click="$emit('update:visible', false)"><img
                src="@/assets/images/component/btn_popup_close.png"
                alt="닫기"/></button>
    </div>

</template>

<script>
    import {EventBus} from "../../../plugins/event-bus";

    export default {
        name: "policy_variable_coldpopup",
        props: {
            visible: {
                type: Boolean,
                require: true,
                default: false
            },
            cold_modal: {
                type: String,
                require: false
            },
            title: {
                type: String,
                require: false,
            },
            filepath: {
                type: String,
                require: false,
            },
            insert_div: {
                type: String,
                require: false,
            },
            index: {
                type: Number,
                require: false
            },
            bf_id: {
                type: Number,
                require: false
            },
        },
        data() {
            return {
                variable_list: [],
                vcheck: this.filepath,
                value: '',
                checkedData: false,
            }
        },
        computed: {
            variableList() {
                return this.variable_list
            }
        },
        methods: {
            handleWrapperClick() {
                this.$emit('update:visible', false)
            },
            get_variable_list() {
                axios.get('/management/policy_variablelist/'
                )
                    .then((response) => {
                        this.variable_list = response.data
                    })
            },
            sendVariable() {
                var bfdiv;
                if (undefined != this.bf_id) {
                    bfdiv = this.bf_id
                } else {
                    bfdiv = undefined
                }

                let data = {
                    file_path: this.value,
                    file_index: this.index,
                    bf_id: this.bf_id
                }
                if (this.cold_modal == 'ocr_modal') {
                    EventBus.$emit('variable_ocr_filepath', data)
                }
                if (this.cold_modal == 'extension_modal') {
                    EventBus.$emit('variable_extensionfile_path', data)
                }
                if (this.cold_modal == 'general_modal') {
                    EventBus.$emit('variable_generalfile_path', data)
                }
                if (this.cold_modal == 'cold_modal') {
                    EventBus.$emit('variable_coldfile_path_cold', data)
                }
                this.$emit('update:visible', false);
            },
            sendFolderVariable() {
                let data = {
                    file_path: this.value,
                    file_index: this.index,
                }
                // EventBus.$emit('variable_coldfolder_path_cold', data)
                // this.$emit('update:visible', false);
            },
        },
        watch: {
            vcheck(val) {
                this.value = val
            }
        },
        created() {
            this.get_variable_list()
        }
    }
</script>

<style scoped>
    .layer_popup {
        display: block;
    }
</style>
