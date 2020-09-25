<template>
  <transition
    name="el-drawer-fade"
    @after-enter="afterEnter"
    @after-leave="afterLeave">
    <div
      v-show="visible"
      class="bu-drawer"
      :class="[
        `bu-drawer--${placement}`,
        {
          'bu-drawer--open': visible
        }
      ]"
    >
      <div 
        class="bu-drawer-mask"
        :style="maskStyle"
        @click.self="close"
      ></div>
      <div 
        class="bu-drawer-content" 
        :style="{
          ...contentStyle,
          ...drawerStyle
        }"
      >
        <div v-if="title"
          class="bu-drawer-header"
          :style="headerStyle"
        >
          {{ title }}
        </div>
        <slot name="title"></slot>
        <div
          class="bu-drawer-body"
          :style="bodyStyle"
        >
          <slot></slot>
        </div>
        <div
          v-if="this.$slots.footer"
          class="bu-drawer-footer"
          :style="footerStyle"
        >
          <slot name="footer"></slot>
        </div>
      </div>
    </div>
  </transition>  
</template>

<script>
export default {
  name: 'BuDrawer',
  props: {
    visible: {
      type: Boolean,
      default: false
    },
    placement: {
      type: String,
      default: 'right'
    },
    width: {
      type: Number,
      default: 300
    },
    height: {
      type: Number,
      default: 300
    },
    onClose: {
      type: Function
    },
    title: {
      type: String
    },
    maskStyle: {
      type: Object,
      default: () => {}
    },
    headerStyle: {
      type: Object,
      default: () => {}
    },
    bodyStyle: {
      type: Object,
      default: () => {}
    },
    drawerStyle: {
      type: Object,
      default: () => {}
    },
    footerStyle: {
      type: Object,
      default: () => {}
    },
    appendToBody: {
      type: Boolean,
      default: true
    }
  },

  watch: {
    visible(val) {
      if (val) {
        this.closed = false;
        this.$emit('open');
        if (this.appendToBody) {
          document.body.appendChild(this.$el);
        }
      } else {
        if (!this.closed) this.$emit('close');
      }
    }
  },

  computed: {
    contentStyle () {
      if (this.placement === 'left' || this.placement === 'right') {
        return {
          width: this.width + 'px',
          height: '100%',
        }
      }

      if (this.placement === 'top' || this.placement === 'bottom') {
        return {
          width: '100%',
          height: this.height + 'px',
        }
      }
    }
  },

  methods: {
    afterEnter() {
      this.$emit('opened');
    },
    afterLeave() {
      this.$emit('closed');
    },
    close () {
      this.$emit('update:visible', false);
      this.onClose && this.onClose()	
    }
  }
}
</script>

<style lang='scss'>
.bu-drawer {
  position: fixed;
  z-index: 1000; 
  &--top {
    top: 0;
    .bu-drawer-content {
      top: 0;
    }
  }
  &--right {
    right: 0;
    .bu-drawer-content {
      right: 0;
    }
  }
  &--bottom {
    bottom: 0;
    .bu-drawer-content {
      bottom: 0;
    }
  }
  &--left {
    left: 0;
    .bu-drawer-content {
      left: 0;
    }
  }
  &--left, &--right{
    top: 0;
    height: 100%;
  }
  &--top, &--bottom{
    left: 0;
    width: 100%;
  }
  &--open {
    width: 100%;
    height: 100%;
    .bu-drawer-mask {
      height: 100%;
      pointer-events: auto;
    }
  }
}
.bu-drawer-mask {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 0;
  background-color: rgba(0,0,0,.45);
  transition: opacity .3s;
  pointer-events: none;
}
.bu-drawer-content {
  position: absolute;
  background: #ffffff;
  height: 100%;
  box-sizing: border-box;
  transition: all .3s;
  .bu-drawer-header {
    padding: 16px 24px;
    border-bottom: 1px solid #f0f0f0;
  }
  .bu-drawer-body {
    padding: 16px 24px;
  }
  .bu-drawer-footer {
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    box-sizing: border-box;
    padding: 16px 24px;
    border-top: 1px solid #f0f0f0;
  }
}

.el-drawer-fade-enter-active, .el-drawer-fade-leave-active {
  transition: .3s;
  .bu-drawer-mask {
    opacity: 1;
  }
  &.bu-drawer--right {
    .bu-drawer-content {
      transform: translateX(0) translateY(0);
    }
  }
}
.el-drawer-fade-enter, .el-drawer-fade-leave-to {
  .bu-drawer-mask {
    opacity: 0;
  }
  &.bu-drawer--top {
    .bu-drawer-content {
      transform: translateY(-100%);
    }
  }
  &.bu-drawer--right {
    .bu-drawer-content {
      transform: translateX(100%);
    }
  }
  &.bu-drawer--bottom {
    .bu-drawer-content {
      transform: translateY(100%);
    }
  }
  &.bu-drawer--left {
    .bu-drawer-content {
      transform: translateX(-100%);
    }
  }
}
</style>
