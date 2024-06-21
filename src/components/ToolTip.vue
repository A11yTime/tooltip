<template>
    <div class="wsi-wrapper">
      <div class="one-wrap">
        <label for="targetField">Recipient</label>
        <input type="text" id="targetField" style="max-width: 70%">
        <button ref="btn" class="helper wsi-button" @click="toggleTooltip" aria-label="Prepopulate recipient">Tooltip</button>
        <div v-if="showTooltip" aria-label="Prepopulate Recipients" role="dialog" class="wsi tooltip" ref="tooltip" @keydown="handleFocusNavigation">
          <div class="tabtrap" tabindex="0"></div>
          <div id="recipients" data-tabtrap="">
            <form @submit.prevent>
              <fieldset class="wsi" tabindex="-1">
                <legend>Select a name:</legend>
                <label>
                  <input type="radio" name="contactList" value="Alice" checked> Alice
                </label>
                <label>
                  <input type="radio" name="contactList" value="Bob"> Bob
                </label>
                <label>
                  <input type="radio" name="contactList" value="Paul"> Paul
                </label>
              </fieldset>
              <p><button @click="confirmSelection" class="wsi-button">Confirm</button></p>
            </form>
          </div>
          <div class="tabtrap" tabindex="0"></div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        showTooltip: false
      };
    },
    methods: {
      toggleTooltip() {
        this.showTooltip = !this.showTooltip;
        if (this.showTooltip) {
          this.$nextTick(() => {
            const tooltip = this.$refs.tooltip;
            const firstFocusableElement = tooltip.querySelector('[tabindex="0"]');
            if (firstFocusableElement) {
              firstFocusableElement.focus();
            }
          });
        }
      },
      confirmSelection() {
        // Handle confirmation logic here
        this.showTooltip = false; // Hide tooltip after confirmation
        this.$refs.btn.focus()
      },
      handleFocusNavigation(event) {
        const tooltip = this.$refs.tooltip;
        const focusableElements = tooltip.querySelectorAll('input[type="text"], button, [tabindex]:not([tabindex="-1"])');
        const firstFocusableElement = focusableElements[0];
        const lastFocusableElement = focusableElements[focusableElements.length - 1];
        const isShiftPressed = event.shiftKey;

        if (!tooltip.contains(event.target)) {
            event.preventDefault();
            firstFocusableElement.focus();
        } else if (event.target === lastFocusableElement && !isShiftPressed) {
            event.preventDefault();
            firstFocusableElement.focus();
        } else if (event.target === firstFocusableElement && isShiftPressed) {
            event.preventDefault();
            lastFocusableElement.focus();
        }
        }
    }
  };
  </script>
  
  <style scoped>
  .wsi-wrapper {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
  
  .one-wrap {
    position: relative;
  }
  
  label {
    margin-right: 10px;
  }
  
  input[type="text"] {
    max-width: 70%;
    margin-right: 10px;
  }
  
  .wsi.tooltip {
    position: absolute;
    top: calc(100% + 10px);
    left: 50%;
    transform: translateX(-50%);
    background-color: #fff;
    border: 1px solid #ccc;
    padding: 10px;
    border-radius: 5px;
    box-shadow: 0 0 5px rgba(0, 0, 0, 0.3);
    z-index: 100;
  }
  
  .wsi.tooltip.active {
    display: block;
  }
  
  .wsi.tooltip::before {
    content: "";
    position: absolute;
    top: -10px;
    left: 50%;
    transform: translateX(-50%);
    border-width: 10px;
    border-style: solid;
    border-color: transparent transparent #ccc transparent;
  }
  
  .tabtrap {
    outline: none;
  }
  </style>
  