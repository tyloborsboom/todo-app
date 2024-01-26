<script>
export default {
  name: "top-bar",
  data() {
    return {
      closeText: "close",
      maximizeText: "web_asset",
      minimizeText: "remove",
      menuText: "menu",
      window: null,
      settingsOpened: false
    };
  },
  mounted() {
    this.window = this.$bridge.getCurrentWindow();
  },

  methods: {
    close() {
      this.window.close();
    },
    minimize() {
      this.window.minimize();
    },
    toggleMaximize() {
      if (this.window.isMaximized()) this.window.unmaximize();
      else this.window.maximize();
    },
    showSettings() {
      this.settingsOpened = true;
    },
    hideSettings() {
      this.settingsOpened = false;
    }
  },
  render() {
    return (
      <div class="topbar">
        <div class="left">
          {/* Inline TopBarButton component */}
          <button onClick={this.showSettings} class="topbar-button">
            {this.menuText}
          </button>
        </div>
        <div class="dragHandle"></div>
        <div class="right">
          <button onClick={this.minimize} class="topbar-button">
            {this.minimizeText}
          </button>
          <button onClick={this.toggleMaximize} class="topbar-button">
            {this.maximizeText}
          </button>
          <button onClick={this.close} class="topbar-button accent">
            {this.closeText}
          </button>
        </div>

        {this.menu}
      </div>
    );
  }
};
</script>

<style scoped>
.topbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #2c3e50; /* Change background color as needed */
  color: white;
  height: 40px;
  padding: 0 10px;
  user-select: none; /* Prevent text selection */
}

.left,
.right {
  display: flex;
  align-items: center;
}

.dragHandle {
  flex-grow: 1;
  height: 10px;
  cursor: move;
}

.topbar-button {
  background: none;
  border: none;
  color: white;
  cursor: pointer;
  margin-right: 10px;
  font-size: 14px; /* Adjust font size as needed */
}

.topbar-button.accent {
  color: #e74c3c; /* Change the accent color as needed */
}

.transition-fade-enter-active,
.transition-fade-leave-active {
  transition: opacity 0.5s;
}

.transition-fade-enter, .transition-fade-leave-to {
  opacity: 0;
}
</style>