<script setup>
  import { ref, onMounted, onBeforeMount } from 'vue';

  import { Notyf } from 'notyf';
  import 'notyf/notyf.min.css';

  const notyf = new Notyf();

  const WEB3FORMS_ACCESS_KEY = "690f7479-134f-4b72-bb61-dca85d8207ff";

  const subject = "New message from Portfolio Contact Form";

  const name = ref("");
  const email = ref("");
  const message = ref("");

  const isLoading = ref(false);


  const submitForm = async() => {

    if(!recaptchaToken.value) {
      notyf.error('Please verify that you are not a robot.');
      return;
    }

    isLoading.value = true;

    try {

      const response = await fetch("https://api.web3forms.com/submit", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json"
        },
        body: JSON.stringify({
          access_key: WEB3FORMS_ACCESS_KEY,
          subject: subject,
          name: name.value,
          email: email.value,
          message: message.value
        })
      });

      const result = await response.json();

      if(result.success) {
        console.log(result)

        isLoading.value = false;
        notyf.success("Message sent!");
        name.value = "";
        email.value = "";
        message.value = "";
      }

    } catch(error) {
      console.log(error);

      isLoading.value = false;
      notyf.error("Failed to send message");

    } finally {

      resetRecaptcha();
    }
  }

  /*recaptcha integration*/

  const SITE_KEY = '6Lf4KvUsAAAAAAVxijDdvVLR9WrChxlrrbL-zIRe';

  const recaptchaContainer = ref(null);
  const recaptchaWidgetId = ref(null);
  const recaptchaToken = ref('');


  function onRecaptchaSuccess(token) {
    recaptchaToken.value = token;
  }

  function onRecaptchaExpired() {
    recaptchaToken.value = '';
  }

  function renderRecaptcha() {
    if(!window.grecaptcha) {
      console.error('reCAPTCHA not loaded');
      return;
    }

    recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
      sitekey: SITE_KEY,
      size: 'normal',
      callback: onRecaptchaSuccess,
      'expired-callback': onRecaptchaExpired
    });
  }

  function resetRecaptcha() {
    if(recaptchaWidgetId.value !== null) {
      window.grecaptcha.reset(recaptchaWidgetId.value);
      recaptchaToken.value = '';
    }
  }

  onMounted(() => {
    const interval = setInterval(() => {
      if(window.grecaptcha && window.grecaptcha.render) {
        renderRecaptcha();
        clearInterval(interval)
      }
    }, 100);

    onBeforeMount(() => {
      clearInterval(interval);
    });
  })

</script>

<template>
	<form id="contact" @submit.prevent="submitForm">
      <div class="text-center mb-5">
        <p class="section-label">Say Hello</p>
        <h2 class="section-title">Contact</h2>
      </div>
        <div class="contact-form-wrap">
          <div class="mb-3">
            <label class="form-label">Full Name</label>
            <input type="text" v-model="name" id="name" name="name" class="form-control" placeholder="Your full name" required />
          </div>
          <div class="mb-3">
            <label class="form-label">Email</label>
            <input type="email" v-model="email" id="email" name="email" class="form-control" placeholder="you@email.com" required />
          </div>
          <div class="mb-4">
            <label class="form-label">Message</label>
            <textarea class="form-control" v-model="message" rows="5" placeholder="Tell me about your project…"></textarea>
          </div>
          <div class="d-flex justify-content-between align-items-center">
            <button type="submit" class="btn-submit" :disabled="isLoading">
              {{ isLoading ? "Sending..." : "Submit" }}
            </button>
            <div ref="recaptchaContainer"></div>
          </div>
        </div>
   </form>
</template>