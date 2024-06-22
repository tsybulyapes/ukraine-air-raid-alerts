<script>
  import { page } from '$app/stores'; 
  import {base} from '$app/paths' 
	
  $: currentPage = $page.url.pathname
  
</script>

<div class="nav">
  <ul>
    <li><a href={`${base}/`} class:active={currentPage === `${base}/`}>UA</a></li>
    <li><a href={`${base}/eng`} class:active={currentPage === `${base}/eng`}>EN</a></li>
  </ul>
</div>

<style>
  .nav {
    position: absolute;
    width: 100%;
    top: 0;
    left: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 80px;
  }
  ul {
    margin: 0;
    padding: 0;
  }
  li {
    margin: 0;
    padding: 0;
    list-style: none;
    display: inline-block;
    margin: 0 10px;
  }
  a {
    font-size: 14px;
  }
  a.active {
    color: #fff;
    opacity: 0.3;
    pointer-events: none;
  }
</style>