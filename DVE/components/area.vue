<template>

    <div class="exmo_area "
         v-bind:style="o_fixed_height?o_style_css:null"
         v-bind:class="[o_height_mode,area_id, {'suspend':area_suspend, 'suspend_on':o_suspend_on, 'suspend_off':(o_suspend_on===false),   'area_pad':area_pad, 'suspend_max':o_suspend_max_on}]"
    >
        <h2> {{area_title}} </h2>


        <div class="area_tool">

            <div class="tool" v-if="!area_disable_fixbut">
                <input v-model="o_fixed_height" type="checkbox" class="exmo_icon_cheackbox "
                       id="check_btn_{{area_id}}_1"
                       autocomplete="off">
                <label class="exmo_button_icon mini" for="check_btn_{{area_id}}_1">
                    <i class="{{o_fixed_height?'icon-shrink2':'icon-enlarge2'}}"></i>
                </label>
            </div>

            <div class="tool" v-if="area_disable_fixbut&&area_disable_fixbut_max">

                <label v-on:click="suspend_max_on"
                       class="exmo_button_icon mini"><i
                        class="{{o_suspend_max_on?'icon-uniE9D9':'icon-uniE9D9'}}"></i></label>
            </div>


            <div class="tool" v-if="area_disable_fixbut">

                <label v-on:click="suspend_on" :title="miniButTitle"
                       class="exmo_button_icon mini">

                    <i v-if="area_id!='attr_panel'" class="{{o_suspend_on?'icon-shrink2':'icon-enlarge2'}}"></i>
                    <i v-if="area_id=='attr_panel'" class="{{o_suspend_on?'icon-pause2':'icon-play3'}}"></i>


                </label>


            </div>


        </div>

        <slot></slot>
    </div>
    <!--<div draggable="true"-->
         <!--class="exmo_drag"-->
         <!--v-if="o_fixed_height"-->
         <!--v-on:dragstart="drag_heigth_start($event)"-->
         <!--v-on:dragend="drag_heigth_end($event)"-->
         <!--v-on:drag="drag_heigth($event)"-->
        <!--&gt;-->
    <!--</div>-->
</template>

<script>

    export default {
        props: ['area_title', "area_id", "area_hight", "area_disable_fixbut", "area_disable_fixbut_max", "area_suspend", "area_pad", "area_init_close", "area_opened"],

        ready: function ()
        {
            if (this["area_hight"] != undefined)
            {
                this.o_height = +this.area_hight;
                this.o_fixed_height = true;
            }

            if (this.area_init_close)
            {

                this.o_suspend_on = false;
                this.area_opened = (this.o_suspend_on === true)
            }
        },

        data(){
            return {
                o_fixed_height: false,
                o_height: 222,
                o_suspend_on: this.area_opened,
                o_suspend_max_on: false,
                o_last_offset: 0,
                o_style_css: {
                    height: "223px"
                },
                o_dragging: false
            }
        },
        computed: {
            o_height_mode: function ()
            {
                if (this.o_fixed_height)
                {
                    return "mod_fixed_height"
                }
                return ""
            },
            miniButTitle: function ()
            {

                if (this.area_id == 'attr_panel')
                {
                    if (this.o_suspend_on)
                    {
                        var t = "最小化并暂停刷新"
                    } else
                    {
                        var t = "展开并恢复刷新"
                    }

                } else
                {

                    if (this.o_suspend_on)
                    {
                        var t = "最小化"
                    } else
                    {
                        var t = "展开"
                    }

                }
                return Lang.from(t)
            }
        },
        methods: {
            suspend_max_on: function ()
            {
                this.o_suspend_max_on = !this.o_suspend_max_on;
                this.o_suspend_on = this.o_suspend_max_on;
                this.area_opened = (this.o_suspend_on === true)

            },
            suspend_on: function ()
            {
                this.o_suspend_on = !this.o_suspend_on;
                this.o_suspend_max_on = false;
                this.area_opened = (this.o_suspend_on === true)
            }
            ,
            drag_heigth: function (e)
            {
                this.o_height += e.screenY - this.o_last_offset;
                this.o_style_css.height = this.o_height + "px";
                this.o_last_offset = e.screenY;
            },
            drag_heigth_start: function (e)
            {

                this.o_last_offset = e.screenY;
                this.o_dragging = true
//                this.o_dragging=false
//
//                window.e = this.o_style_css
//                this.o_height += e.offsetY - this.o_last_offset
//                this.o_last_offset = e.offsetY;
//                this.o_style_css.height = this.o_height + "px";
//                console.log(this.o_height, e)
            }
            ,
            drag_heigth_end: function (e)
            {
                this.o_dragging = false

                console.log(e.screenY, e)
                this.o_height += e.screenY - this.o_last_offset;
                this.o_style_css.height = this.o_height + "px";
//
//                window.e = this.o_style_css
//                this.o_height += e.offsetY - this.o_last_offset
//                this.o_last_offset = e.offsetY;
//                this.o_style_css.height = this.o_height + "px";
//                console.log(this.o_height, e)
            }


        }

    }


</script>

<style lang="scss" rel="stylesheet/scss">


    .area_tool .tool {
        display: inline-block;
    }

    .exmo_drag {
        background: rgba(255, 0, 0, 0.0);
        height: 6px;
        width: 100%;
        display: block;
        position: relative;
        margin-top: -4px;
        cursor: n-resize;
    }

    .exmo_area {
        position: relative;
        overflow: hidden;
        transition: all .2s;

        /*transition: all .5s;*/

        &.mod_fixed_height {
            /*height: 200px;*/
            overflow: scroll;
            overflow-x: hidden;
        }

    }

    .exmo_area:hover .area_tool {
        display: block;
    }

    .area_tool {
        position: absolute;
        top: 9px;
        right: 10px;
        display: none;

        .exmo_button_icon i {
            color: #9E9E9E;
            font-size: 11px;
        }

        .exmo_icon_cheackbox:checked + .exmo_button_icon {
            border: none;
            background: rgba(80, 80, 80, 0.09);
            margin-right: -2px;
        }

        .exmo_button_icon.mini {
            padding: 0 6px;
            padding-bottom: 2px;
        }
    }

    .attr_panel .area_tool {
        .exmo_button_icon i {
            vertical-align: middle;
            display: inline-block;
            margin-top: -1px;
        }
    }

    .exmo_area.suspend {
        position: fixed;
        bottom: 49px;
        width: calc(100% - 32px);
        z-index: 9;
        background: #F0F0F0;
        border-top: 2px solid rgba(0, 0, 0, 0.08);
        height: 6px;

        &.suspend_on {
            box-shadow: 0 -4px 16px rgba(0, 0, 0, 0.07);
            height: 30%;
        }

        &.suspend_on.suspend_max {
            height: calc(100% - 100px);
        }
    }

    .exmo_area.area_pad {
        max-height: 6px;

        &.suspend_on {
            max-height: 999px;
        }
    }


</style>


