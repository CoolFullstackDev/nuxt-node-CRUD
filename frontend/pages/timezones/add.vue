<template>
  <div class="w-full flex justify-center pt-10 md:pt-20">
    <div class="w-full max-w-xs">
      <validation-observer
        ref="observer"
        v-slot="{ invalid }"
        tag="form"
        class="bg-white shadow-md px-8 pt-6 pb-8 mb-4"
        @submit.prevent="create"
      >
        <div class="mb-4">
          <TextField
            v-model="form.name"
            rules="required"
            name="name"
            label="Name"
            placeholder="EDT"
          />
        </div>
        <div class="mb-4">
          <TextField
            v-model="form.city"
            rules="required"
            name="city"
            label="City"
            placeholder="New York"
          />
        </div>
        <div class="mb-4">
          <TextField
            v-model="form.differenceToGMT"
            rules="required|timezone_difference"
            name="differenceToGMT"
            label="Different To GMT"
            placeholder="-4"
          />
        </div>
        <div class="flex items-center justify-between">
          <Button
            :disabled="invalid"
            text="Create"
            primary
            medium
            type="submit"
          />
        </div>
      </validation-observer>
    </div>
  </div>
</template>

<script>
import Button from "@/components/Button"
import TextField from "@/components/TextField"

export default {
  middleware: "auth",
  components: {
    Button,
    TextField,
  },
  data() {
    return {
      form: {
        name: "",
        city: "",
        differenceToGMT: "",
      },
    }
  },
  methods: {
    async create() {
      const isValid = await this.$refs.observer.validate()
      if (!isValid) return

      try {
        await this.$store.dispatch("createTimezone", this.form)
        this.$swal({
          position: "top-end",
          icon: "success",
          title: "Successfully created",
          showConfirmButton: false,
          toast: true,
          timer: 2000,
        })

        // clear search keyword from store
        this.$store.commit("setSearch", "")
        this.$router.push("/timezones")
      } catch (err) {
        this.$swal({
          position: "top-end",
          icon: "error",
          title: err.response.data.message,
          showConfirmButton: false,
          toast: true,
          timer: 2000,
        })
      }
    },
  },
}
</script>

<style></style>
