<script lang="ts">
    import { getContext } from 'svelte';
    import { toast } from 'svelte-sonner';
    import { readable, writable } from 'svelte/store';

    // Import your existing stores
    import {
        WEBUI_NAME,
        chatId,
        mobile,
        settings,
        showArchivedChats,
        showSettings,
        showSidebar,
        user
    } from '$lib/stores';

    // Define a store for tracking the remaining words
    // export const remainingWords = writable(0);
    import { remainingWords } from '$lib/stores';

    import { slide } from 'svelte/transition';
    import ShareChatModal from '../chat/ShareChatModal.svelte';
    import ModelSelector from '../chat/ModelSelector.svelte';
    import Tooltip from '../common/Tooltip.svelte';
    import Menu from './Navbar/Menu.svelte';
    import { page } from '$app/stores';
    import UserMenu from './Sidebar/UserMenu.svelte';
    import MenuLines from '../icons/MenuLines.svelte';
    import AdjustmentsHorizontal from '../icons/AdjustmentsHorizontal.svelte';

    const i18n = getContext('i18n');

    export let initNewChat: Function;
    export let title: string = $WEBUI_NAME;
    export let shareEnabled: boolean = false;

    export let chat;
    export let selectedModels;

    export let showModelSelector = true;
    export let showControls = false;

    let showShareChatModal = false;
    let showDownloadChatModal = false;
</script>

<ShareChatModal bind:show={showShareChatModal} chatId={$chatId} />
<nav id="nav" class=" sticky py-2.5 top-0 flex flex-row justify-center z-10">
    <div class=" flex max-w-full w-full mx-auto px-5 pt-0.5 md:px-[1rem]">
        <div class="flex items-center w-full max-w-full">
            <!-- Sidebar Toggle Button -->
            <div class="{$showSidebar
                    ? 'md:hidden'
                    : ''} mr-3 self-start flex flex-none items-center text-gray-600 dark:text-gray-400">
                <button
                    id="sidebar-toggle-button"
                    class="cursor-pointer px-2 py-2 flex rounded-xl hover:bg-gray-50 dark:hover:bg-gray-850 transition"
                    on:click={() => {
                        showSidebar.set(!$showSidebar);
                    }}>
                    <div class=" m-auto self-center"><MenuLines /></div>
                </button>
            </div>
            
            
            <!-- Remaining Words Chipset -->
            <div class="flex items-center mr-3">
                <a href="https://matn.ai/finance/create_pay" target="_blank" title="Top-up Charge">
                    <div class="chip">
                        {$remainingWords} {$i18n.t('Words')}
                    </div>
                </a>
            </div>
            
			
            <!-- Model Selector and Other Controls -->
            <div class="flex-1 overflow-hidden max-w-full">
                {#if showModelSelector}
                    <ModelSelector bind:selectedModels showSetDefault={!shareEnabled} />
                {/if}
            </div>
			
            <!-- Other Action Buttons -->
            <div class="self-start flex flex-none items-center text-gray-600 dark:text-gray-400">
                {#if shareEnabled && chat && chat.id}
                    <Menu
                        {chat}
                        {shareEnabled}
                        shareHandler={() => {
                            showShareChatModal = !showShareChatModal;
                        }}
                        downloadHandler={() => {
                            showDownloadChatModal = !showDownloadChatModal;
                        }}>
                        <button
                            class="hidden md:flex cursor-pointer px-2 py-2 rounded-xl hover:bg-gray-50 dark:hover:bg-gray-850 transition"
                            id="chat-context-menu-button">
                            <div class=" m-auto self-center">
                                <svg
                                    xmlns="http://www.w3.org/2000/svg"
                                    fill="none"
                                    viewBox="0 0 24 24"
                                    stroke-width="1.5"
                                    stroke="currentColor"
                                    class="size-5">
                                    <path
                                        stroke-linecap="round"
                                        stroke-linejoin="round"
                                        d="M6.75 12a.75.75 0 1 1-1.5 0 .75.75 0 0 1 1.5 0ZM12.75 12a.75.75 0 1 1-1.5 0 .75.75 0 0 1 1.5 0ZM18.75 12a.75.75 0 1 1-1.5 0 .75.75 0 0 1 1.5 0Z"
                                    />
                                </svg>
                            </div>
                        </button>
                    </Menu>
                {/if}

                <Tooltip content={$i18n.t('Controls')}>
                    <button
                        class=" flex cursor-pointer px-2 py-2 rounded-xl hover:bg-gray-50 dark:hover:bg-gray-850 transition"
                        on:click={() => {
                            showControls = !showControls;
                        }}>
                        <div class=" m-auto self-center">
                            <AdjustmentsHorizontal className=" size-5" strokeWidth="0.5" />
                        </div>
                    </button>
                </Tooltip>

                <Tooltip content={$i18n.t('New Chat')}>
                    <button
                        id="new-chat-button"
                        class=" flex {$showSidebar
                            ? 'md:hidden'
                            : ''} cursor-pointer px-2 py-2 rounded-xl text-gray-600 dark:text-gray-400 hover:bg-gray-50 dark:hover:bg-gray-850 transition"
                        on:click={() => {
                            initNewChat();
                        }}>
                        <div class=" m-auto self-center">
                            <svg
                                xmlns="http://www.w3.org/2000/svg"
                                viewBox="0 0 20 20"
                                fill="currentColor"
                                class="w-5 h-5">
                                <path
                                    d="M5.433 13.917l1.262-3.155A4 4 0 017.58 9.42l6.92-6.918a2.121 2.121 0 013 3l-6.92 6.918c-.383.383-.84.685-1.343.886l-3.154 1.262a.5.5 0 01-.65-.65z"
                                />
                                <path
                                    d="M3.5 5.75c0-.69.56-1.25 1.25-1.25H10A.75.75 0 0010 3H4.75A2.75 2.75 0 002 5.75v9.5A2.75 2.75 0 004.75 18h9.5A2.75 2.75 0 0017 15.25V10a.75.75 0 00-1.5 0v5.25c0 .69-.56 1.25-1.25 1.25h-9.5c-.69 0-1.25-.56-1.25-1.25v-9.5z"
                                />
                            </svg>
                        </div>
                    </button>
                </Tooltip>

                {#if $user !== undefined}
                    <UserMenu
                        className="max-w-[200px]"
                        role={$user.role}
                        on:show={(e) => {
                            if (e.detail === 'archived-chat') {
                                showArchivedChats.set(true);
                            }
                        }}>
                        <button
                            class="select-none flex rounded-xl p-1.5 w-full hover:bg-gray-50 dark:hover:bg-gray-850 transition"
                            aria-label="User Menu">
                            <div class=" self-center">
                                <img
                                    src={$user.profile_image_url}
                                    class="size-6 object-cover rounded-full"
                                    alt="User profile"
                                    draggable="false" />
                            </div>
                        </button>
                    </UserMenu>
                {/if}
            </div>
        </div>
    </div>
</nav>

<style>
    .chip {
        background-color: #e0e0e0;
        border-radius: 16px;
        display: inline-block;
        padding: 0 12px;
        height: 32px;
        font-size: 14px;
        line-height: 32px;
        color: #555;
    }
</style>