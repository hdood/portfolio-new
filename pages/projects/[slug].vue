<template>
  <div>
    <AppHeader
      :title="project.name"
      :description="project.description"
    />

    <div class="max-w-4xl mx-auto space-y-8">
      <!-- Project Info -->
      <div class="flex items-center gap-4 flex-wrap">
        <UBadge
          :label="project.status"
          :color="project.status === 'Active' ? 'green' : 'gray'"
          variant="subtle"
        />
        <UBadge
          v-if="project.opensource"
          label="Open Source"
          color="blue"
          variant="subtle"
        />
      </div>

      <!-- Main Content (from markdown) -->
      <ContentRenderer v-if="project.body" :value="project" class="prose dark:prose-invert max-w-none" />

      <!-- Screenshots/Videos Section -->
      <div v-if="project.media && project.media.length > 0" class="space-y-4">
        <h2 class="text-2xl font-bold">Media</h2>
        <div class="grid grid-cols-1 gap-6">
          <div v-for="(item, index) in project.media" :key="index" class="rounded-lg overflow-hidden border border-gray-200 dark:border-gray-800">
            <!-- Video -->
            <video
              v-if="item.type === 'video'"
              :src="item.url"
              controls
              class="w-full max-h-[600px] object-contain bg-black"
              :poster="item.poster"
            >
              Your browser does not support the video tag.
            </video>
            <!-- Image -->
            <NuxtImg
              v-else
              :src="item.url"
              :alt="item.caption || `${project.name} screenshot`"
              class="w-full"
              loading="lazy"
            />
            <!-- Caption -->
            <p v-if="item.caption" class="text-sm text-gray-600 dark:text-gray-400 p-3 bg-gray-50 dark:bg-gray-900">
              {{ item.caption }}
            </p>
          </div>
        </div>
      </div>

      <!-- Technologies Used -->
      <div v-if="project.technologies && project.technologies.length > 0" class="space-y-4">
        <h2 class="text-2xl font-bold">Technologies</h2>
        <div class="flex flex-wrap gap-2">
          <UBadge
            v-for="tech in project.technologies"
            :key="tech"
            :label="tech"
            variant="soft"
          />
        </div>
      </div>

      <!-- External Link -->
      <div v-if="project.url" class="pt-6 border-t border-gray-200 dark:border-gray-800">
        <UButton
          :to="project.url"
          target="_blank"
          external
          icon="i-heroicons-arrow-top-right-on-square"
          size="lg"
          trailing
        >
          Visit {{ project.name }}
        </UButton>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const route = useRoute();
const slug = route.params.slug as string;

const { data: project } = await useAsyncData(`project-${slug}`, () =>
  queryContent('projects').where({ slug }).findOne()
);

if (!project.value) {
  throw createError({
    statusCode: 404,
    message: 'Project not found',
  });
}

useSeoMeta({
  title: project.value.name,
  description: project.value.description,
  ogImage: project.value.thumbnail,
  ogTitle: project.value.name,
  ogDescription: project.value.description,
  twitterCard: 'summary_large_image',
});
</script>
