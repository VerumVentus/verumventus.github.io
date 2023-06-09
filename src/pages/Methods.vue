<template>
  <nav class="section-nav h-fit">
    <ol class="bullet-list">
      <li v-for="link in links">
        <a :href="link.target">{{ link.name }}</a>
      </li>
    </ol>
  </nav>
</template>

<script setup>
import { onMounted, onUnmounted, ref } from 'vue';

const observer = ref(null);
const root = ref(null);
const links = ref([]);

onMounted(() => {
  observer.value = new IntersectionObserver(onElementObserved, {
    threshold: 0.5,
    rootMargin: '-60px 0px',
  });

  root.value.querySelectorAll('section[id]').forEach((section) => {
    observer.value.observe(section);
  });

  links.value = Array.from(
    root.value.querySelectorAll('section[id]'),
    (section) => ({
      name: section.querySelector('h2').textContent,
      target: `#${section.getAttribute('id')}`,
    })
  );
});

onUnmounted(() => {
  observer.value.disconnect();
});

function onElementObserved(entries) {
  entries.forEach(({ target, isIntersecting }) => {
    const id = target.getAttribute('id');
    // make it so only one element can be active at a time even if multiple are intersecting
    if (isIntersecting) {
      root.value
        .querySelector(`nav li a[href="#${id}"]`)
        .parentElement.classList.add('active');
    } else {
      root.value
        .querySelector(`nav li a[href="#${id}"]`)
        .parentElement.classList.remove('active');
    }
  });
}
</script>

<style scoped>
/* Sidebar Navigation */

[id]::before {
  content: '';
  display: block;
  height: 75px;
  margin-top: -75px;
  visibility: hidden;
}
.section-nav {
  position: sticky;
  top: 150px;
  right: 0;
  margin-top: 2rem;
  padding-left: 0;
  border-left: 1px solid #efefef;
}

.section-nav a {
  text-decoration: none;
  display: block;
  padding: 0.125rem 0;
  color: black;
  transition: all 50ms ease-in-out;
}

.section-nav a:hover,
.section-nav a:focus {
  color: #666;
}

.section-nav li.active > a {
  color: #f2765d;
  font-weight: 500;
}

/* Sticky Navigation */
main > nav {
  position: sticky;
  top: 2rem;
  align-self: start;
}

ul.bullet-list,
ol.bullet-list {
  list-style: none;
  margin: 0;
  padding: 0;
}
li {
  margin-left: 1rem;
}

/** page layout **/
main {
  display: grid;
  position: relative;
  grid-template-columns: 1fr 15em;
  max-width: 100em;
  width: 90%;
  margin: 0 auto;
}

/** enlarge the sections for this demo, so that we have a long scrollable page **/
section {
  margin-bottom: 50rem;
}
</style>
