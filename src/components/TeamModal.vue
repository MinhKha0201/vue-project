<script setup lang="ts">
import { Button } from '@/components/ui/button'
import {
    Dialog,
    DialogContent,
    DialogDescription,
    DialogFooter,
    DialogHeader,
    DialogTitle,
    DialogTrigger,
    DialogClose,
} from '@/components/ui/dialog'
import {
    ComboboxAnchor,
    ComboboxContent,
    ComboboxEmpty,
    ComboboxGroup,
    ComboboxInput,
    ComboboxItem,
    ComboboxItemIndicator,
    ComboboxLabel,
    ComboboxRoot,
    ComboboxSeparator,
    ComboboxTrigger,
    ComboboxViewport,
} from 'radix-vue'
import type { Team } from '../types/Team.type'
import { Icon } from '@iconify/vue'
import { ref } from 'vue'

type Prop = {
    userId: number
    teams: Team[]
    teamsSelected: Team[]
}

const props = defineProps<Prop>()
const emit = defineEmits(['update-teams-data'])

const selected = ref<Team[]>([])

const handleClick = () => {
    emit('update-teams-data', { userId: props.userId, data: selected.value })
}

const selectTeam = (value: Team[]) => {
    selected.value = value
}
</script>

<template lang="">
    <Dialog>
        <DialogTrigger as-child>
            <Button variant="outline" class="w-[100px]">
                {{
                    teamsSelected.length > 0
                        ? teamsSelected.length + ' ' + 'Selected'
                        : 'Select Teams'
                }}
            </Button>
        </DialogTrigger>
        <DialogContent
            class="sm:max-w-[425px] grid-rows-[auto_minmax(0,1fr)_auto] p-0 max-h-[90dvh]"
        >
            <DialogHeader class="p-6 pb-0">
                <DialogTitle>Teams</DialogTitle>
                <DialogDescription> Select teams </DialogDescription>
            </DialogHeader>
            <ComboboxRoot
                class="relative px-6"
                multiple
                @update:modelValue="selectTeam"
                :defaultValue="teamsSelected"
            >
                <ComboboxAnchor
                    class="w-full inline-flex items-center justify-between rounded px-[15px] text-[13px] leading-none h-[35px] gap-[5px] bg-white text-grass11 shadow-[0_2px_10px] shadow-black/10 hover:bg-mauve3 focus:shadow-[0_0_0_2px] focus:shadow-black data-[placeholder]:text-grass9 outline-none"
                >
                    <ComboboxInput
                        class="!bg-transparent outline-none text-grass11 h-full selection:bg-grass5 placeholder-mauve8"
                        placeholder="Select Teams"
                    />
                    <ComboboxTrigger>
                        <Icon
                            icon="radix-icons:chevron-down"
                            class="h-4 w-4 text-grass11"
                        />
                    </ComboboxTrigger>
                </ComboboxAnchor>

                <ComboboxContent
                    class="absolute z-10 w-full mt-2 max-w-[375px] bg-white overflow-hidden rounded shadow-[0px_10px_38px_-10px_rgba(22,_23,_24,_0.35),_0px_10px_20px_-15px_rgba(22,_23,_24,_0.2)] will-change-[opacity,transform] data-[side=top]:animate-slideDownAndFade data-[side=right]:animate-slideLeftAndFade data-[side=bottom]:animate-slideUpAndFade data-[side=left]:animate-slideRightAndFade"
                >
                    <ComboboxViewport class="p-[5px]">
                        <ComboboxEmpty
                            class="text-mauve8 text-xs font-medium text-center py-2"
                        />

                        <ComboboxGroup>
                            <ComboboxLabel
                                class="px-[25px] text-sm leading-[25px] text-mauve11"
                            >
                                Teams
                            </ComboboxLabel>

                            <ComboboxItem
                                v-for="team in teams"
                                :key="team.id"
                                class="text-sm leading-none text-grass11 rounded-[3px] flex items-center h-[25px] pr-[35px] pl-[25px] py-4 relative select-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:outline-none data-[highlighted]:bg-grass9 data-[highlighted]:text-grass1 hover:bg-slate-50"
                                :value="team"
                            >
                                <ComboboxItemIndicator
                                    class="absolute left-0 w-[25px] inline-flex items-center justify-center"
                                >
                                    <Icon icon="radix-icons:check" />
                                </ComboboxItemIndicator>
                                <span>
                                    {{ team.name }}
                                </span>
                            </ComboboxItem>
                            <ComboboxSeparator
                                class="h-[1px] bg-grass6 m-[5px]"
                            />
                        </ComboboxGroup>
                    </ComboboxViewport>
                </ComboboxContent>
            </ComboboxRoot>
            <DialogFooter class="p-6 pt-0">
                <DialogClose>
                    <Button @click="handleClick" type="submit">
                        Save changes
                    </Button>
                </DialogClose>
            </DialogFooter>
        </DialogContent>
    </Dialog>
</template>
