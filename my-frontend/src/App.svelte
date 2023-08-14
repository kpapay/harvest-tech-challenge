<script lang="ts">
import Users from './lib/Users.svelte';
import Home from './lib/Home.svelte';
import Menu from './lib/Menu.svelte';

import { onMount } from 'svelte';
import { Route } from 'tinro';
import { PaginationItem } from 'flowbite-svelte';

let p = 1;
let pSize = 100;
let pTotal = 10;
let users;
let helper = { current: p, pages: pTotal, size: pSize, total: 0 };

function displayUsers(pNo, pSize) {

  const query = `query ($page: Int, $pageSize: Int, $username: String) {
    Users(pagination: {page: $page, pageSize: $pageSize}, filter: {username: $username}) {
      data {
        id
        username
        companies {
        id
        name
        rooms {
          id
          name
        }
      }
      }
      meta {
        pagination {
          page
          pageSize
          totalOfPage
          totalOfRecord
        }
      }
    }
  }`;

  const variables = { "page": pNo, "pageSize": pSize };

  fetch('https://rnd-ns2-tech-challenge-next-be.vercel.app/api/graphql', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ query, variables }),
  }).then(response => response.json())
  .then(respobj => {
    //console.log(respobj);
    users = respobj.data.Users.data;

    let page = respobj.data.Users.meta.pagination.page;
    let pageSize = respobj.data.Users.meta.pagination.pageSize;
    let totalOfPage = respobj.data.Users.meta.pagination.totalOfPage;
    let totalOfRecord = respobj.data.Users.meta.pagination.totalOfRecord;

    helper.current = page;
    helper.pages = totalOfPage;
    helper.size = pageSize;
    helper.total = totalOfRecord;
  })
  .catch(error => console.error(error));
  
}

onMount(async () => {
  displayUsers(p, pSize);
});

const previous = () => {
  if (p>1) {
    p--;
    displayUsers(p, pSize);
  }
 };

const next = () => {
  if (p<helper.pages) {
    p++;
    displayUsers(p, pSize);
  }
};

</script>

<Menu/>

<Route path="/">
  <Home/>
</Route>

<Route path="/users">
  <h1>Harvest Tech Challenge</h1>
  <br/>
  <div class="flex space-x-3 items-center justify-center">
    <PaginationItem class="flex items-center gap-2 text-white bg-gray-800" on:click={previous}>Previous</PaginationItem>
    <PaginationItem class="flex items-center gap-2 text-white bg-gray-800" on:click={next}>Next</PaginationItem>
    <div class="text-sm text-gray-700 dark:text-gray-400">
      Showing page <span class="font-semibold text-gray-900 dark:text-white">{helper.current}</span> of
      <span class="font-semibold text-gray-900 dark:text-white">{helper.pages}</span> pages (page size: 
      <span class="font-semibold text-gray-900 dark:text-white">{helper.size}</span>, total entries:
      <span class="font-semibold text-gray-900 dark:text-white">{helper.total}</span>)
    </div>
  </div>
  <br/>
  <Users {users} />
</Route>

<style>
  .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
  .read-the-docs {
    color: #888;
  }
</style>
