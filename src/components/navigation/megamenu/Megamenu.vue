 <template>              
                 <!-- Megamenu -->
                    <ul class="megamenu d-flex flex-nowrap">
                      <li><strong>General</strong>
                        <ul>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> School Settings</a></li>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> User Accounts</a></li>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> Manage Menu</a></li>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> Code Book</a></li>
                        </ul>
                      </li>
                      <li><strong>Reports</strong>
                        <ul>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> PMF Measures</a></li>
                        </ul>
                      </li>
                      <li><strong>Classes</strong>
                        <ul>
                          <li><a [routerLinkActive]="['active']" [routerLink]="'/home'"><i class="icon icon-arrow-gray"></i> Home Room</a></li>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> School Year</a></li>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> Subject Manager</a></li>
                        </ul>
                      </li>
                      <li><strong>Notification</strong>
                        <ul>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> Manage Template</a></li>
                          <li><a href="#"><i class="icon icon-arrow-gray"></i> Notification Email List</a></li>
                        </ul>
                      </li>
                      <li><strong>Project Commit</strong>

                      </li>
                    </ul>
                 <!-- Megamenu-END -->
 </template> 