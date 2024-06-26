<template>
  <div class="sign-in-page">
    <form action="#" class="sign-in-page__form">
      <h2 class="sign-in-page__headline">Sign In</h2>
      <label class="sign-in-page__email-input-label" for="emailInput">
        Enter your email below:
      </label>
      <input
        v-model="email"
        type="email"
        name="email"
        class="sign-in-page__email-input"
        id="emailInput"
        placeholder="Email"
        required
      />
      <label class="sign-in-page__password-input-label" for="passwordInput">
        Enter your password below:
      </label>
      <input
        v-model="password"
        type="password"
        name="password"
        class="sign-in-page__password-input"
        id="passwordInput"
        placeholder="Password"
        required
      />
      <button
        class="sign-in-page__confirm-btn"
        @click.prevent="onConfirmBtnClicked"
        :disabled="isLoading"
      >
        Confirm
      </button>
    </form>
    <h3 class="sign-in-page__sign-up-suggestion-headline">
      Don't have an account yet?
    </h3>
    <a
      class="sign-in-page__sign-up-btn"
      @click="this.$router.push('/sign-up')"
      :class="{ 'disabled-link': isLoading }"
    >
      Sign up
    </a>
  </div>
</template>

<script>
import toastMixin from "@/mixins/toastMixin.js";
import errorMsgMixin from "@/mixins/errorMsgMixin.js";
import { signInUser } from "@/services/firebase";

export default {
  name: "sign-in-page",

  mixins: [toastMixin, errorMsgMixin],

  data() {
    return {
      isLoading: false,
      email: "",
      password: "",
    };
  },

  methods: {
    async onConfirmBtnClicked() {
      this.isLoading = true;
      this.setLoadingToast("Logging...");
      try {
        await signInUser(this.email, this.password);

        this.setSuccessToast("You have successfully logged in!");

        this.email = "";
        this.password = "";

        this.$router.push("/");
      } catch (error) {
        const errorMsg = this.getErrorMsg(error);

        this.setErrorToast(
          `An error occurred while trying to log in to account! ${errorMsg}`
        );
      } finally {
        this.isLoading = false;
      }
    },
  },
};
</script>

<style lang="scss" scoped>
@import "@/styles/global";

.sign-in-page {
  flex-grow: 1;
  @extend %default-wrapper;
  display: flex;
  flex-direction: column;
  align-items: center;
  &__form {
    display: flex;
    flex-direction: column;
    .sign-in-page__headline {
      @include default-headline(42px, 42px, $color-black);
      margin-bottom: 30px;
      text-align: center;
    }
    .sign-in-page__email-input-label {
      @include default-text(24px, 24px, $color-black);
      @media (max-width: $phone-l) {
        font-size: 20px;
        line-height: 20px;
      }
    }
    .sign-in-page__email-input {
      @extend %default-input;
      margin-bottom: 20px;
    }
    .sign-in-page__password-input-label {
      @include default-text(24px, 24px, $color-black);
      @media (max-width: $phone-l) {
        font-size: 20px;
        line-height: 20px;
      }
    }
    .sign-in-page__password-input {
      @extend %default-input;
      margin-bottom: 40px;
    }
    .sign-in-page__confirm-btn {
      align-self: center;
      @include default-btn(300px, $color-white, $color-orange);
      margin-bottom: 20px;
    }
  }
  &__sign-up-suggestion-headline {
    @include default-text(20px, 20px, $color-black);
    margin-bottom: 5px;
  }
  &__sign-up-btn {
    @include default-btn(100px, $color-white, $color-orange);
  }
}
</style>
