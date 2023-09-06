<script setup lang="ts">
import WelcomeItem from './WelcomeItem.vue'
import DocumentationIcon from './icons/IconDocumentation.vue'
import ToolingIcon from './icons/IconTooling.vue'
import EcosystemIcon from './icons/IconEcosystem.vue'
import CommunityIcon from './icons/IconCommunity.vue'
import SupportIcon from './icons/IconSupport.vue'
</script>

<template>
  <WelcomeItem>
    <template #icon>
      <DocumentationIcon />
    </template>
    <template #heading>参考资料</template>
    著作权所有权归原作者所有，仅提供参考资料，不做任何商业用途。
    <ul>
      <li><a href="https://5echm.kagangtuya.top/">5e不全书</a></li>
    </ul>
  </WelcomeItem>

  <WelcomeItem>
    <template #icon>
      <ToolingIcon />
    </template>
    <template #heading><RouterLink to="/ability_randomizer">人物属性roll点工具</RouterLink></template>
  </WelcomeItem>

  <WelcomeItem>
    <template #icon>
      <CommunityIcon />
    </template>
    <template #heading>问题与讨论</template>
    <a href="https://github.com/jixing-ica/ica-dnd-helper/issues">github issues</a>
  </WelcomeItem>

  <WelcomeItem>
    <template #icon>
      <SupportIcon />
    </template>
    <template #heading>支持我们</template>

    别急，还没有
  </WelcomeItem>
</template>
