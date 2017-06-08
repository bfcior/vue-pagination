<template>
    <ul class="pagination">
        <li v-if="pagination.current_page > 1">
            <a href="#" aria-label="Previous" @click.prevent="change(pagination.current_page - 1)">
                <span aria-hidden="true">&laquo;</span>
            </a>
        </li>
        <li v-if="pagination.current_page == 1 && pagination.total_pages > 0" class="disabled">
            <span>«</span>
        </li>

        <li v-for="element in slider" :class="[ element == pagination.current_page ? 'active' : (element != '...' ? '' : 'disabled')]">
            <a v-if="element != '...'" href="#" @click.prevent="change(element)">{{ element }}</a>
            <span v-else>...</span>
        </li>

        <li v-if="pagination.current_page < pagination.total_pages">
            <a href="#" aria-label="Next" @click.prevent="change(pagination.current_page + 1)">
                <span aria-hidden="true">&raquo;</span>
            </a>
        </li>
        <li v-if="pagination.total_pages > 0 && pagination.current_page == pagination.total_pages" class="disabled">
            <span>»</span>
        </li>
    </ul>
</template>

<script>
    function range(start, end) {
        let array = [];

        for (let i = start; i <= end; i++) {
            array.push(i);
        }

        return array;
    }

    export default {
        props: {
            pagination: {
                type: Object,
                required: true
            },
            offset: {
                type: Number,
                default: 10
            }
        },
        computed: {
            slider: function () {
                if (!this.pagination.total_pages) {
                    return [];
                }

                let begin = function() {
                    return [1, 2, '...'];
                };

                let vm = this;
                let end = function() {
                    return ['...', vm.pagination.total_pages - 1, vm.pagination.total_pages];
                };

                if (this.pagination.total_pages < (this.offset * 2) + 6) {
                    return range(1, this.pagination.total_pages);
                }

                let window = this.offset * 2;

                if (this.pagination.current_page <= window) {
                    return range(1, window + 2).concat(end());
                }

                if (this.pagination.current_page > (this.pagination.total_pages - window)) {
                    return begin().concat(range(this.pagination.total_pages - (window + 2), this.pagination.total_pages));
                }

                let slider = begin().concat(range(this.pagination.current_page - this.offset, this.pagination.current_page + this.offset));
                return slider.concat(end());
            }
        },
        methods: {
            change: function(page) {
                Event.$emit('pagination.current_page', page);
                this.callback();
            }
        }
    };
</script>
