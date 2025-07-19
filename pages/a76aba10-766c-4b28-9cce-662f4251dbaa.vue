<template>
    <Suspense>
      <EffectComposerPmndrs>
        <!--VignettePmndrs :darkness="0.9" :offset="0.2" /-->
        <GlitchPmndrs />
      </EffectComposerPmndrs>
    </Suspense>
</template>

<script setup lang="js">
import { EffectComposerPmndrs, GlitchPmndrs, VignettePmndrs } from '@tresjs/post-processing';
</script>