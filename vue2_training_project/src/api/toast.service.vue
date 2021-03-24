<script lang="ts">

import { Component, Vue } from 'vue-property-decorator';

@Component
export default class ToastService extends Vue {

    public showToast(
        message: string,
        title: string,
        variant = 'danger',
        append = false
    ) {

        this.$bvToast.toast(`${message}`, {
            title,
            autoHideDelay: 5000,
            variant: variant,
            appendToast: append,
        })
    }

}
</script>