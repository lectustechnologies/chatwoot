<template>
  <div class="agent-table-container">
    <ve-table
      max-height="calc(100vh - 35rem)"
      :fixed-header="true"
      :columns="columns"
      :table-data="tableData"
    />
    <div v-if="isLoading" class="agents-loader">
      <spinner />
      <span>{{
        $t('OVERVIEW_REPORTS.AGENT_CONVERSATIONS.LOADING_MESSAGE')
      }}</span>
    </div>
    <empty-state
      v-else-if="!isLoading && !agentMetrics.length"
      :title="$t('OVERVIEW_REPORTS.AGENT_CONVERSATIONS.NO_AGENTS')"
    />
    <div v-if="agentMetrics.length > 0" class="table-pagination">
      <ve-pagination
        :total="totalAgents"
        :page-index="pageIndex"
        :page-size="10"
        :page-size-option="[10]"
        @on-page-number-change="onPageNumberChange"
      />
    </div>
  </div>
</template>

<script>
import { mixin as clickaway } from 'vue-clickaway';
import { VeTable, VePagination } from 'vue-easytable';

import Spinner from 'shared/components/Spinner.vue';
import EmptyState from 'dashboard/components/widgets/EmptyState.vue';
import timeMixin from 'dashboard/mixins/time';

export default {
  name: 'AgentTable',
  components: {
    EmptyState,
    Spinner,
    VeTable,
    VePagination,
  },
  mixins: [clickaway, timeMixin],
  props: {
    totalAgents: {
      type: Number,
      default: 0,
    },
    agentMetrics: {
      type: Array,
      default: () => [],
    },
    isLoading: {
      type: Boolean,
      default: false,
    },
    pageIndex: {
      type: Number,
      default: 1,
    },
  },
  data() {
    return {};
  },
  computed: {
    tableData() {
      return this.agentMetrics.map(agent => {
        return {
          agent: agent.user.name || '---',
          total: agent.metric.total || 0,
          unattended: agent.metric.unattended || 0,
          status: agent.user.availability,
        };
      });
    },
    columns() {
      return [
        {
          field: 'agent',
          key: 'agent',
          title: 'Agent',
          fixed: 'left',
          align: 'left',
          width: 20,
        },
        {
          field: 'total',
          key: 'total',
          title: 'Total',
          align: 'left',
          width: 15,
        },
        {
          field: 'unattended',
          key: 'unattended',
          title: 'Unattended',
          align: 'left',
          width: 15,
        },
        {
          field: 'status',
          key: 'status',
          title: 'Status',
          align: 'left',
          width: 10,
        },
      ];
    },
  },
  mounted() {},
  methods: {
    onPageNumberChange(pageIndex) {
      this.$emit('page-change', pageIndex);
    },
  },
};
</script>

<style lang="scss" scoped>
.agent-table-container {
  display: flex;
  flex-direction: column;
  flex: 1;

  .ve-table {
    &::v-deep {
      th.ve-table-header-th {
        font-size: var(--font-size-mini) !important;
        padding: var(--space-small) var(--space-two) !important;
      }

      td.ve-table-body-td {
        padding: var(--space-small) var(--space-two) !important;
      }
    }
  }

  &::v-deep .ve-pagination {
    background-color: transparent;
  }

  &::v-deep .ve-pagination-select {
    display: none;
  }

  .emoji-response {
    font-size: var(--font-size-large);
  }

  .table-pagination {
    margin-top: var(--space-normal);
    text-align: right;
  }
}

.agents-loader {
  align-items: center;
  display: flex;
  font-size: var(--font-size-default);
  justify-content: center;
  padding: var(--space-large);
}
</style>
