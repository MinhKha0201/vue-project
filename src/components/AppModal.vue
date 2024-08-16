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
} from '@/components/ui/dialog'
import { TableBody, TableCell, TableRow } from '@/components/ui/table'
import type { App } from '@/types/App.type'
import { Checkbox } from './ui/checkbox'
import TableCustom from '@/components/Custom/TableCustom.vue'
import { Trash2 } from 'lucide-vue-next'

type Prop = {
    userId: number
    apps: App[]
}

defineProps<Prop>()
</script>

<template lang="">
    <Dialog>
        <DialogTrigger as-child>
            <Button variant="outline"> Select Apps </Button>
        </DialogTrigger>
        <DialogContent
            class="sm:max-w-[425px] grid-rows-[auto_minmax(0,1fr)_auto] p-0 max-h-[90dvh] !max-w-5xl"
        >
            <DialogHeader class="p-6 pb-0">
                <DialogTitle>Apps</DialogTitle>
                <DialogDescription> Select Apps </DialogDescription>
            </DialogHeader>
            <div class="grid grid-cols-2">
                <div class="">
                    <div class="p-4">
                        <Checkbox id="CheckAll" />
                        <label for="CheckAll" class="ml-8">Select All</label>
                    </div>
                    <TableCustom class="max-h-[600px] h-4/5">
                        <TableBody>
                            <TableRow v-for="app in apps" :key="app.id">
                                <TableCell class="font-medium">
                                    <Checkbox />
                                </TableCell>
                                <TableCell>{{ app.name }}</TableCell>
                            </TableRow>
                        </TableBody>
                    </TableCustom>
                </div>
                <div class="grid">
                    <div class="flex items-center justify-between px-4 py-3">
                        <p>None Selected</p>
                        <Button class="bg-red-500 hover:bg-red-600 mr-6"
                            >Delete All</Button
                        >
                    </div>
                    <TableCustom class="max-h-[600px]">
                        <TableBody>
                            <TableRow v-for="app in apps" :key="app.id">
                                <TableCell>{{ app.name }}</TableCell>
                                <TableCell class="text-center">
                                    <Button
                                        size="xs"
                                        class="bg-red-500 hover:bg-red-600 px-3 py-2 text-xs"
                                        ><Trash2 size="16"
                                    /></Button>
                                </TableCell>
                            </TableRow>
                        </TableBody>
                    </TableCustom>
                </div>
            </div>
            <DialogFooter class="p-6 pt-0">
                <Button type="submit"> Save changes </Button>
            </DialogFooter>
        </DialogContent>
    </Dialog>
</template>
