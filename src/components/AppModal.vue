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
import type { App } from '@/types/App.type'
import { Checkbox } from './ui/checkbox'
import { Trash2 } from 'lucide-vue-next'
import { computed, ref } from 'vue'
import { Input } from '@/components/ui/input'

type Prop = {
    userId: number
    apps: App[]
    appSelected: App[]
}

const props = defineProps<Prop>()
const emit = defineEmits(['update-apps-data'])

const selected = ref<App[]>(props.appSelected)
const searchValue = ref<string>('')

const computedData = computed<(App & { isCheck: boolean })[]>(() => {
    if (searchValue.value === '' && searchValue.value.length === 0) {
        return props.apps.map((app) => {
            return {
                ...app,
                isCheck: selected.value.some((a) => a.app_id === app.app_id),
            }
        })
    } else {
        return props.apps
            .map((app) => {
                return {
                    ...app,
                    isCheck: selected.value.some(
                        (a) => a.app_id === app.app_id,
                    ),
                }
            })
            .filter((app) =>
                app.name
                    .toLowerCase()
                    .includes(searchValue.value.toLowerCase()),
            )
    }
})
const isCheckAll = ref<boolean>(
    computedData.value.some((app) => app.isCheck === false),
)

const handleCheck = (check: boolean, app_id: string) => {
    const app = computedData.value.find((app) => app.app_id === app_id)
    if (app) {
        app.isCheck = check
    }
    if (check) {
        const app = props.apps.find((a) => a.app_id === app_id)
        if (app) {
            selected.value.push(app)
        }
    } else {
        isCheckAll.value = false
        selected.value = selected.value.filter((app) => app.app_id !== app_id)
    }
}

const handleCheckAll = (check: boolean) => {
    computedData.value.forEach((app) => (app.isCheck = check))
    isCheckAll.value = check
    if (check) {
        const apps = computedData.value.filter(
            (app) => !selected.value.some((a) => a.app_id === app.app_id),
        )
        selected.value.push(...apps)
    } else {
        selected.value = selected.value.filter(
            (app) => !computedData.value.some((a) => a.app_id === app.app_id),
        )
    }
}

const handleClick = () => {
    emit('update-apps-data', { userId: props.userId, data: selected.value })
}

const handleDelete = (id: string) => {
    selected.value = selected.value.filter((app) => app.app_id !== id)
    const app = computedData.value.find((app) => app.app_id === id)
    if (app) {
        app.isCheck = false
    }
    isCheckAll.value = false
}

const handleDeleteAll = () => {
    selected.value = []
    computedData.value.forEach((app) => (app.isCheck = false))
    isCheckAll.value = false
}

const handleSearch = (event: Event) => {
    setTimeout(async () => {
        const target = event.target as HTMLInputElement
        searchValue.value = target.value
    }, 500)
    if (selected.value.length !== computedData.value.length) {
        isCheckAll.value = false
    }
}
</script>

<template lang="">
    <Dialog>
        <DialogTrigger as-child>
            <Button variant="outline" class="w-[100px]">
                {{
                    appSelected.length > 0
                        ? `${appSelected.length} Selected`
                        : 'Select Apps'
                }}
            </Button>
        </DialogTrigger>
        <DialogContent
            class="sm:max-w-[425px] flex flex-col p-0 max-h-screen !max-w-5xl"
        >
            <DialogHeader class="p-6 pb-0">
                <DialogTitle>Apps</DialogTitle>
                <DialogDescription> Select Apps </DialogDescription>
            </DialogHeader>
            <div class="grid grid-cols-2 flex-1">
                <div class="p-4 h-full">
                    <div class="mb-4">
                        <Input
                            v-model="searchValue"
                            type="text"
                            placeholder="Search"
                            @input="handleSearch"
                        />
                    </div>
                    <div class="pb-4">
                        <Checkbox
                            id="CheckAll"
                            @update:checked="handleCheckAll"
                            :checked="isCheckAll"
                        />
                        <label for="CheckAll" class="ml-8">Select All</label>
                    </div>
                    <ul class="max-h-[500px] overflow-x-auto">
                        <li
                            v-for="app in computedData"
                            :key="app.app_id"
                            class="flex gap-8 items-center py-2"
                        >
                            <Checkbox
                                :id="app.app_id"
                                :checked="app.isCheck"
                                @update:checked="
                                    (check: boolean) =>
                                        handleCheck(check, app.app_id)
                                "
                            />
                            <label :for="app.app_id">{{ app.name }}</label>
                        </li>
                    </ul>
                </div>
                <div class="mr-6 h-full">
                    <div
                        class="flex items-center justify-between px-4 py-3 border-[1px]"
                    >
                        <p>
                            {{
                                selected.length > 0
                                    ? selected.length + ' ' + 'Selected'
                                    : 'None Selected'
                            }}
                        </p>
                        <Button
                            @click="handleDeleteAll"
                            class="bg-red-500 hover:bg-red-600 mr-6"
                            >Delete All</Button
                        >
                    </div>
                    <ul class="max-h-[550px] overflow-x-auto border-x-[1px]">
                        <li
                            v-for="app in selected"
                            :key="app.app_id"
                            class="flex justify-between items-center py-2 px-4 hover:cursor-default"
                        >
                            <p>{{ app.name }}</p>
                            <Button
                                @click="() => handleDelete(app.app_id)"
                                size="xs"
                                class="bg-red-500 hover:bg-red-600"
                                ><Trash2 size="16"
                            /></Button>
                        </li>
                    </ul>
                </div>
            </div>
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
