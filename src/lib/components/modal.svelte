<script lang="ts">
    import { Form } from '$lib/elements/forms';
    import { disableCommands } from '$lib/commandCenter';
    import { beforeNavigate } from '$app/navigation';
    import { Alert, Layout, Modal } from '@appwrite.io/pink-svelte';

    export let show = false;
    export let autoClose = true;
    export let error: string = null;
    export let dismissible = true;
    export let size: 's' | 'm' | 'l' = 'm';
    export let onSubmit: (e: SubmitEvent) => Promise<void> | void = function () {
        return;
    };
    export let title = '';
    export let hideFooter = false;

    let alert: HTMLElement;

    beforeNavigate(() => {
        if (autoClose) show = false;
    });

    $: $disableCommands(show);

    $: if (error) {
        alert?.scrollIntoView({ behavior: 'smooth', block: 'start', inline: 'nearest' });
    }
</script>

<Form isModal {onSubmit}>
    <Modal {size} {title} bind:open={show} {hideFooter} {dismissible}>
        <slot slot="description" name="description" />
        {#if error}
            <div bind:this={alert}>
                <Alert.Inline
                    dismissible
                    status="warning"
                    on:dismiss={() => {
                        error = null;
                    }}>
                    {error}
                </Alert.Inline>
            </div>
        {/if}
        <slot />
        <svelte:fragment slot="footer">
            <Layout.Stack direction="row" justifyContent="flex-end">
                <slot name="footer" />
            </Layout.Stack>
        </svelte:fragment>
    </Modal>
</Form>

<style>
    /* temporary fix to modal width */
    :global(dialog section) {
        max-width: 100% !important;
    }
</style>
