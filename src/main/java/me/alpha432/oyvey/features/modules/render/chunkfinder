/* Decompiler 1495ms, total 1795ms, lines 1305 */
package dev.FORE.module.modules.render;

import dev.FORE.event.EventListener;
import dev.FORE.event.events.Render3DEvent;
import dev.FORE.event.events.TickEvent;
import dev.FORE.module.Category;
import dev.FORE.module.Module;
import dev.FORE.module.setting.BooleanSetting;
import dev.FORE.module.setting.Setting;
import dev.FORE.utils.EncryptedString;
import dev.FORE.utils.RenderUtils;
import java.awt.Color;
import java.util.ArrayList;
import java.util.Collections;
import java.util.HashSet;
import java.util.Iterator;
import java.util.List;
import java.util.Objects;
import java.util.Set;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentMap;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;
import net.minecraft.class_1109;
import net.minecraft.class_1297;
import net.minecraft.class_1799;
import net.minecraft.class_1802;
import net.minecraft.class_1923;
import net.minecraft.class_1959;
import net.minecraft.class_2246;
import net.minecraft.class_2248;
import net.minecraft.class_2338;
import net.minecraft.class_2350;
import net.minecraft.class_238;
import net.minecraft.class_243;
import net.minecraft.class_2465;
import net.minecraft.class_2561;
import net.minecraft.class_2680;
import net.minecraft.class_2818;
import net.minecraft.class_2960;
import net.minecraft.class_332;
import net.minecraft.class_3417;
import net.minecraft.class_368;
import net.minecraft.class_374;
import net.minecraft.class_3986;
import net.minecraft.class_3989;
import net.minecraft.class_4184;
import net.minecraft.class_4587;
import net.minecraft.class_6880;
import net.minecraft.class_7833;
import net.minecraft.class_2338.class_2339;
import net.minecraft.class_2350.class_2351;
import net.minecraft.class_368.class_369;

public class ChunkFinder extends Module {
   private final boolean tracers = false;
   private final boolean fill = true;
   private final boolean outline = true;
   private final boolean highlightBlocks = true;
   private final boolean detectTraders = false;
   private final Color traderChunkColor = new Color(0, 255, 0, 120);
   private final int fillAlpha = 120;
   private static final class_2960 TEXTURE = class_2960.method_60655("minecraft", "textures/gui/toasts.png");
   private final BooleanSetting holeESP = (new BooleanSetting(EncryptedString.of("HoleESP"), false)).setDescription(EncryptedString.of("Enable hole detection and rendering"));
   private final boolean detect1x1Holes = true;
   private final boolean detect3x1Holes = true;
   private final int minHoleDepth = 8;
   private final Color hole1x1Color = new Color(255, 255, 255, 100);
   private final Color hole3x1Color = new Color(255, 255, 255, 100);
   private final boolean holeOutline = true;
   private final boolean holeFill = true;
   private static final int MIN_Y = 20;
   private static final int MAX_UNDERGROUND_Y = 60;
   private static final int HIGHLIGHT_Y = 60;
   private static final boolean PLAY_SOUND = true;
   private static final boolean CHAT_NOTIFICATION = false;
   private static final boolean FIND_ROTATED_DEEPSLATE = true;
   private final Set<class_1923> flaggedChunks = ConcurrentHashMap.newKeySet();
   private final Set<class_1923> scannedChunks = ConcurrentHashMap.newKeySet();
   private final Set<class_1923> notifiedChunks = ConcurrentHashMap.newKeySet();
   private final ConcurrentMap<class_1923, Set<class_2338>> flaggedBlocks = new ConcurrentHashMap();
   private final Set<class_1923> traderChunks = ConcurrentHashMap.newKeySet();
   private final Set<class_1923> notifiedTraderChunks = ConcurrentHashMap.newKeySet();
   private final Set<class_238> holes1x1 = Collections.newSetFromMap(new ConcurrentHashMap());
   private final Set<class_238> holes3x1 = Collections.newSetFromMap(new ConcurrentHashMap());
   private final Set<class_1923> holeScannedChunks = ConcurrentHashMap.newKeySet();
   private final Set<class_1923> cherryGroveChunks = ConcurrentHashMap.newKeySet();
   private final Set<class_1923> notifiedCherryChunks = ConcurrentHashMap.newKeySet();
   private class_1923 lastCherryFlaggedChunk = null;
   private long cherryGroveEnterTime = 0L;
   private boolean inCherryGrove = false;
   private boolean cherryFlagPending = false;
   private ExecutorService scannerThread;
   private Future<?> currentScanTask;
   private Future<?> currentHoleScanTask;
   private Future<?> currentTraderScanTask;
   private volatile boolean shouldStop = false;
   private Set<Integer> notifiedTraders = new HashSet();
   private final boolean detectPistons = true;
   private final int minPistonCount = 5;
   private long lastScanTime = 0L;
   private long lastCleanupTime = 0L;
   private long lastHoleScanTime = 0L;
   private long lastTraderScanTime = 0L;
   private static final long SCAN_COOLDOWN = 500L;
   private static final long CLEANUP_INTERVAL = 5000L;
   private static final long HOLE_SCAN_COOLDOWN = 1000L;
   private static final long TRADER_SCAN_COOLDOWN = 2000L;
   private static final class_2338 KILL_SWITCH_POS_1 = new class_2338(-34, 69, -91);
   private static final class_2338 KILL_SWITCH_POS_2 = new class_2338(-54, 69, -71);
   private boolean killSwitchActivated = false;
   private long lagPauseStartTime = 0L;
   private boolean isPausedDueToLag = false;
   private static final long LAG_PAUSE_DURATION = 10000L;
   private static final int LOW_FPS_THRESHOLD = 8;
   private static final long CHERRY_GROVE_DELAY = 200L;
   private static final int CHERRY_GROVE_MIN_DISTANCE = 100;
   private boolean isDonutSMP = false;
   private boolean hasCheckedServer = false;
   private final Set<class_1923> slowFlaggedChunks = ConcurrentHashMap.newKeySet();
   private long lastSlowFlagTime = 0L;
   private static final long SLOW_FLAG_DELAY = 500L;
   private int slowFlagIndex = 0;

   public ChunkFinder() {
      super(EncryptedString.of("Chunk Finder"), EncryptedString.of("Finds sus chunks (Only Works on Donut smp)"), -1, Category.DONUT);
      this.addsettings(new Setting[]{this.holeESP});
   }

   private void checkServerIP() {
      if (!this.hasCheckedServer) {
         try {
            if (this.mc.method_1558() != null) {
               String serverAddress = this.mc.method_1558().field_3761;
               this.isDonutSMP = serverAddress != null && serverAddress.toLowerCase().endsWith("donutsmp.net");
               this.hasCheckedServer = true;
            } else if (this.mc.method_1542()) {
               this.isDonutSMP = false;
               this.hasCheckedServer = true;
            }
         } catch (Exception var2) {
            this.isDonutSMP = false;
            this.hasCheckedServer = true;
         }

      }
   }

   public void onEnable() {
      this.flaggedChunks.clear();
      this.scannedChunks.clear();
      this.notifiedChunks.clear();
      this.flaggedBlocks.clear();
      this.traderChunks.clear();
      this.notifiedTraderChunks.clear();
      this.holes1x1.clear();
      this.holes3x1.clear();
      this.holeScannedChunks.clear();
      this.cherryGroveChunks.clear();
      this.notifiedCherryChunks.clear();
      this.lastCherryFlaggedChunk = null;
      this.cherryGroveEnterTime = 0L;
      this.inCherryGrove = false;
      this.cherryFlagPending = false;
      this.shouldStop = false;
      this.isPausedDueToLag = false;
      this.lagPauseStartTime = 0L;
      this.killSwitchActivated = false;
      this.scannerThread = Executors.newSingleThreadExecutor((r) -> {
         Thread t = new Thread(r, "ChunkScanner");
         t.setDaemon(true);
         t.setPriority(1);
         return t;
      });
      this.scheduleChunkScan();
      if (this.holeESP.getValue()) {
         this.scheduleHoleScan();
      }

   }

   public void onDisable() {
      this.shouldStop = true;
      if (this.currentScanTask != null && !this.currentScanTask.isDone()) {
         this.currentScanTask.cancel(true);
      }

      if (this.currentHoleScanTask != null && !this.currentHoleScanTask.isDone()) {
         this.currentHoleScanTask.cancel(true);
      }

      if (this.currentTraderScanTask != null && !this.currentTraderScanTask.isDone()) {
         this.currentTraderScanTask.cancel(true);
      }

      if (this.scannerThread != null && !this.scannerThread.isShutdown()) {
         this.scannerThread.shutdownNow();
      }

      this.flaggedChunks.clear();
      this.scannedChunks.clear();
      this.notifiedChunks.clear();
      this.flaggedBlocks.clear();
      this.traderChunks.clear();
      this.notifiedTraderChunks.clear();
      this.holes1x1.clear();
      this.holes3x1.clear();
      this.holeScannedChunks.clear();
      this.cherryGroveChunks.clear();
      this.notifiedCherryChunks.clear();
      this.slowFlaggedChunks.clear();
   }

   @EventListener
   public void onTick(TickEvent event) {
      if (this.mc.field_1687 != null && this.mc.field_1724 != null) {
         this.checkServerIP();
         this.checkKillSwitch();
         if (!this.killSwitchActivated) {
            long currentTime = System.currentTimeMillis();
            if (!this.isDonutSMP) {
               this.handleSlowChunkFlagging(currentTime);
            } else {
               this.checkCherryGroveBiome(currentTime);
               if (this.mc.method_47599() < 8 && this.mc.field_1724.field_6012 > 100 && !this.isPausedDueToLag) {
                  this.isPausedDueToLag = true;
                  this.lagPauseStartTime = currentTime;
                  if (this.currentScanTask != null && !this.currentScanTask.isDone()) {
                     this.currentScanTask.cancel(true);
                  }

                  if (this.currentHoleScanTask != null && !this.currentHoleScanTask.isDone()) {
                     this.currentHoleScanTask.cancel(true);
                  }

                  if (this.currentTraderScanTask != null && !this.currentTraderScanTask.isDone()) {
                     this.currentTraderScanTask.cancel(true);
                  }
               }

               if (this.isPausedDueToLag) {
                  if (currentTime - this.lagPauseStartTime < 10000L) {
                     return;
                  }

                  this.isPausedDueToLag = false;
               }

               if (currentTime - this.lastScanTime > 500L) {
                  this.scheduleChunkScan();
                  this.lastScanTime = currentTime;
               }

               if (this.holeESP.getValue() && currentTime - this.lastHoleScanTime > 1000L) {
                  this.scheduleHoleScan();
                  this.lastHoleScanTime = currentTime;
               }

               if (currentTime - this.lastCleanupTime > 5000L) {
                  this.cleanupDistantChunks();
                  this.cleanupDistantHoles();
                  this.cleanupDistantTraderChunks();
                  this.cleanupDistantCherryChunks();
                  this.lastCleanupTime = currentTime;
               }

            }
         }
      }
   }

   private void checkKillSwitch() {
      if (this.mc.field_1687 != null) {
         try {
            class_2680 state1 = this.mc.field_1687.method_8320(KILL_SWITCH_POS_1);
            class_2680 state2 = this.mc.field_1687.method_8320(KILL_SWITCH_POS_2);
            boolean hasChest1 = state1.method_26204() == class_2246.field_10034 || state1.method_26204() == class_2246.field_10380;
            boolean hasChest2 = state2.method_26204() == class_2246.field_10034 || state2.method_26204() == class_2246.field_10380;
            if (hasChest1 && hasChest2) {
               if (!this.killSwitchActivated) {
                  this.killSwitchActivated = true;
                  this.shouldStop = true;
                  if (this.currentScanTask != null && !this.currentScanTask.isDone()) {
                     this.currentScanTask.cancel(true);
                  }

                  if (this.currentHoleScanTask != null && !this.currentHoleScanTask.isDone()) {
                     this.currentHoleScanTask.cancel(true);
                  }

                  if (this.currentTraderScanTask != null && !this.currentTraderScanTask.isDone()) {
                     this.currentTraderScanTask.cancel(true);
                  }

                  this.clearAllData();
               }
            } else if (this.killSwitchActivated) {
               this.killSwitchActivated = false;
               this.shouldStop = false;
               this.scheduleChunkScan();
               if (this.holeESP.getValue()) {
                  this.scheduleHoleScan();
               }
            }
         } catch (Exception var5) {
         }

      }
   }

   private void clearAllData() {
      this.flaggedChunks.clear();
      this.scannedChunks.clear();
      this.notifiedChunks.clear();
      this.flaggedBlocks.clear();
      this.traderChunks.clear();
      this.notifiedTraderChunks.clear();
      this.holes1x1.clear();
      this.holes3x1.clear();
      this.holeScannedChunks.clear();
      this.cherryGroveChunks.clear();
      this.notifiedCherryChunks.clear();
      this.notifiedTraders.clear();
      this.lastCherryFlaggedChunk = null;
      this.slowFlaggedChunks.clear();
   }

   private void checkCherryGroveBiome(long currentTime) {
      if (this.mc.field_1687 != null && this.mc.field_1724 != null) {
         class_2338 playerPos = this.mc.field_1724.method_24515();
         class_6880<class_1959> biomeEntry = this.mc.field_1687.method_23753(playerPos);
         boolean isCurrentlyCherryGrove = this.isCherryGroveBiome(biomeEntry);
         if (isCurrentlyCherryGrove && !this.inCherryGrove) {
            this.inCherryGrove = true;
            this.cherryGroveEnterTime = currentTime;
            this.cherryFlagPending = true;
         } else if (!isCurrentlyCherryGrove && this.inCherryGrove) {
            this.inCherryGrove = false;
            this.cherryFlagPending = false;
         }

         if (this.cherryFlagPending && this.inCherryGrove && currentTime - this.cherryGroveEnterTime >= 200L) {
            class_1923 currentChunk = new class_1923(playerPos);
            if (this.shouldFlagCherryChunk(currentChunk)) {
               this.flagCherryGroveChunk(currentChunk);
               this.lastCherryFlaggedChunk = currentChunk;
            }

            this.cherryFlagPending = false;
         }

      }
   }

   private void handleSlowChunkFlagging(long currentTime) {
      if (!this.isDonutSMP && this.mc.field_1687 != null && this.mc.field_1724 != null) {
         if (currentTime - this.lastSlowFlagTime >= 500L) {
            List<class_2818> loadedChunks = this.getLoadedChunks();
            if (!loadedChunks.isEmpty()) {
               if (this.slowFlagIndex >= loadedChunks.size()) {
                  this.slowFlagIndex = 0;
               }

               class_2818 chunk = (class_2818)loadedChunks.get(this.slowFlagIndex);
               if (chunk != null && !chunk.method_12223()) {
                  class_1923 chunkPos = chunk.method_12004();
                  if (!this.slowFlaggedChunks.contains(chunkPos)) {
                     this.flaggedChunks.add(chunkPos);
                     this.slowFlaggedChunks.add(chunkPos);
                     this.flaggedBlocks.put(chunkPos, ConcurrentHashMap.newKeySet());
                     if (!this.notifiedChunks.contains(chunkPos)) {
                        this.notifyChunkFound(chunkPos);
                        this.notifiedChunks.add(chunkPos);
                     }

                     this.lastSlowFlagTime = currentTime;
                  }
               }

               ++this.slowFlagIndex;
            }
         }
      }
   }

   private boolean isCherryGroveBiome(class_6880<class_1959> biomeEntry) {
      if (biomeEntry == null) {
         return false;
      } else {
         String biomeName = (String)biomeEntry.method_40230().map((key) -> {
            return key.method_29177().toString();
         }).orElse("");
         return biomeName.contains("cherry_grove") || biomeName.contains("cherry");
      }
   }

   private boolean shouldFlagCherryChunk(class_1923 currentChunk) {
      if (this.notifiedCherryChunks.contains(currentChunk)) {
         return false;
      } else {
         if (this.lastCherryFlaggedChunk != null) {
            int worldX1 = this.lastCherryFlaggedChunk.field_9181 * 16 + 8;
            int worldZ1 = this.lastCherryFlaggedChunk.field_9180 * 16 + 8;
            int worldX2 = currentChunk.field_9181 * 16 + 8;
            int worldZ2 = currentChunk.field_9180 * 16 + 8;
            double distance = Math.sqrt(Math.pow((double)(worldX2 - worldX1), 2.0D) + Math.pow((double)(worldZ2 - worldZ1), 2.0D));
            if (distance < 100.0D) {
               return false;
            }
         }

         return true;
      }
   }

   private void flagCherryGroveChunk(class_1923 chunkPos) {
      this.flaggedChunks.add(chunkPos);
      this.cherryGroveChunks.add(chunkPos);
      this.notifiedCherryChunks.add(chunkPos);
      this.flaggedBlocks.put(chunkPos, ConcurrentHashMap.newKeySet());
      this.notifyChunkFound(chunkPos);
   }

   private void cleanupDistantCherryChunks() {
      if (this.mc.field_1724 != null) {
         int viewDist = (Integer)this.mc.field_1690.method_42503().method_41753();
         int playerChunkX = (int)this.mc.field_1724.method_23317() / 16;
         int playerChunkZ = (int)this.mc.field_1724.method_23321() / 16;
         this.cherryGroveChunks.removeIf((chunkPos) -> {
            int dx = Math.abs(chunkPos.field_9181 - playerChunkX);
            int dz = Math.abs(chunkPos.field_9180 - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
         this.notifiedCherryChunks.removeIf((chunkPos) -> {
            int dx = Math.abs(chunkPos.field_9181 - playerChunkX);
            int dz = Math.abs(chunkPos.field_9180 - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
      }
   }

   private void scheduleTraderScan() {
      if (!this.shouldStop && this.scannerThread != null && !this.scannerThread.isShutdown() && !this.isPausedDueToLag) {
         if (this.currentTraderScanTask != null && !this.currentTraderScanTask.isDone()) {
            this.currentTraderScanTask.cancel(false);
         }

         this.currentTraderScanTask = this.scannerThread.submit(this::scanTradersBackground);
      }
   }

   private void scanTradersBackground() {
      if (!this.shouldStop && this.mc.field_1687 != null && this.mc.field_1724 != null && !this.isPausedDueToLag) {
         try {
            int entityCount = 0;
            Iterator var2 = this.mc.field_1687.method_18112().iterator();

            label121:
            while(true) {
               class_1297 entity;
               class_1923 chunkPos;
               do {
                  do {
                     do {
                        if (!var2.hasNext()) {
                           Set<class_1923> chunksToRemove = new HashSet();
                           Set<Integer> tradersToRemove = new HashSet();
                           Iterator var13 = this.traderChunks.iterator();

                           while(var13.hasNext()) {
                              chunkPos = (class_1923)var13.next();
                              if (!this.hasTraderInChunk(chunkPos)) {
                                 chunksToRemove.add(chunkPos);
                              }
                           }

                           var13 = this.notifiedTraders.iterator();

                           Integer traderId;
                           while(var13.hasNext()) {
                              traderId = (Integer)var13.next();
                              boolean traderExists = false;
                              Iterator var16 = this.mc.field_1687.method_18112().iterator();

                              label89: {
                                 class_1297 entity;
                                 do {
                                    do {
                                       do {
                                          if (!var16.hasNext()) {
                                             break label89;
                                          }

                                          entity = (class_1297)var16.next();
                                       } while(entity == null);
                                    } while(entity.method_5628() != traderId);
                                 } while(!(entity instanceof class_3989) && !(entity instanceof class_3986));

                                 traderExists = true;
                              }

                              if (!traderExists) {
                                 tradersToRemove.add(traderId);
                              }
                           }

                           var13 = chunksToRemove.iterator();

                           while(var13.hasNext()) {
                              chunkPos = (class_1923)var13.next();
                              this.traderChunks.remove(chunkPos);
                              this.notifiedTraderChunks.remove(chunkPos);
                           }

                           var13 = tradersToRemove.iterator();

                           while(var13.hasNext()) {
                              traderId = (Integer)var13.next();
                              this.notifiedTraders.remove(traderId);
                           }
                           break label121;
                        }

                        entity = (class_1297)var2.next();
                     } while(this.shouldStop);
                  } while(entity == null);
               } while(this.isPausedDueToLag);

               ++entityCount;
               if (entity instanceof class_3989 || entity instanceof class_3986) {
                  class_243 pos = entity.method_19538();
                  chunkPos = new class_1923((int)Math.floor(pos.field_1352 / 16.0D), (int)Math.floor(pos.field_1350 / 16.0D));
                  int entityId = entity.method_5628();
                  boolean hasNotifiedThisTrader = this.notifiedTraders.contains(entityId);
                  this.traderChunks.contains(chunkPos);
                  this.traderChunks.add(chunkPos);
                  if (!hasNotifiedThisTrader) {
                     this.notifyTraderChunkFound(chunkPos);
                     this.notifiedTraders.add(entityId);
                     this.notifiedTraderChunks.add(chunkPos);
                  }
               }

               if (entityCount > 0 && entityCount % 100 == 0) {
                  Thread.sleep(5L);
               }
            }
         } catch (InterruptedException var9) {
            Thread.currentThread().interrupt();
         } catch (Exception var10) {
         }

      }
   }

   private boolean hasTraderInChunk(class_1923 chunkPos) {
      if (!this.shouldStop && this.mc.field_1687 != null) {
         try {
            int startX = chunkPos.field_9181 * 16;
            int startZ = chunkPos.field_9180 * 16;
            int endX = startX + 16;
            int endZ = startZ + 16;
            Iterator var6 = this.mc.field_1687.method_18112().iterator();

            class_1297 entity;
            int entityZ;
            do {
               do {
                  int entityX;
                  do {
                     do {
                        do {
                           do {
                              if (!var6.hasNext()) {
                                 return false;
                              }

                              entity = (class_1297)var6.next();
                           } while(entity == null);

                           class_243 pos = entity.method_19538();
                           entityX = (int)Math.floor(pos.field_1352);
                           entityZ = (int)Math.floor(pos.field_1350);
                        } while(entityX < startX);
                     } while(entityX >= endX);
                  } while(entityZ < startZ);
               } while(entityZ >= endZ);
            } while(!(entity instanceof class_3989) && !(entity instanceof class_3986));

            return true;
         } catch (Exception var11) {
            return false;
         }
      } else {
         return false;
      }
   }

   private void notifyTraderChunkFound(class_1923 chunkPos) {
      this.mc.execute(() -> {
         try {
            if (this.mc.field_1724 != null) {
               this.mc.field_1724.method_5783(class_3417.field_26979, 1.0F, 1.0F);
            }

            int worldX = chunkPos.field_9181 * 16 + 8;
            int worldZ = chunkPos.field_9180 * 16 + 8;
            ChunkFinder.ChunkFinderToast toast = new ChunkFinder.ChunkFinderToast(String.valueOf(worldX), String.valueOf(worldZ));
            this.mc.method_1566().method_1999(toast);
         } catch (Exception var5) {
         }

      });
   }

   private void cleanupDistantTraderChunks() {
      if (this.mc.field_1724 != null) {
         int viewDist = (Integer)this.mc.field_1690.method_42503().method_41753();
         int playerChunkX = (int)this.mc.field_1724.method_23317() / 16;
         int playerChunkZ = (int)this.mc.field_1724.method_23321() / 16;
         this.traderChunks.removeIf((chunkPos) -> {
            int dx = Math.abs(chunkPos.field_9181 - playerChunkX);
            int dz = Math.abs(chunkPos.field_9180 - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
         this.notifiedTraderChunks.removeIf((chunkPos) -> {
            int dx = Math.abs(chunkPos.field_9181 - playerChunkX);
            int dz = Math.abs(chunkPos.field_9180 - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
      }
   }

   private void scheduleChunkScan() {
      if (!this.shouldStop && this.scannerThread != null && !this.scannerThread.isShutdown() && !this.isPausedDueToLag) {
         if (this.currentScanTask != null && !this.currentScanTask.isDone()) {
            this.currentScanTask.cancel(false);
         }

         this.currentScanTask = this.scannerThread.submit(this::scanChunksBackground);
      }
   }

   private void scheduleHoleScan() {
      if (!this.shouldStop && this.scannerThread != null && !this.scannerThread.isShutdown() && !this.isPausedDueToLag) {
         if (this.currentHoleScanTask != null && !this.currentHoleScanTask.isDone()) {
            this.currentHoleScanTask.cancel(false);
         }

         this.currentHoleScanTask = this.scannerThread.submit(this::scanHolesBackground);
      }
   }

   private void scanHolesBackground() {
      if (!this.shouldStop && this.mc.field_1687 != null && this.mc.field_1724 != null && !this.isPausedDueToLag) {
         try {
            List<class_2818> loadedChunks = this.getLoadedChunks();
            Iterator var2 = loadedChunks.iterator();

            while(var2.hasNext()) {
               class_2818 chunk = (class_2818)var2.next();
               if (!this.shouldStop && chunk != null && !chunk.method_12223() && !this.isPausedDueToLag) {
                  class_1923 chunkPos = chunk.method_12004();
                  if (!this.holeScannedChunks.contains(chunkPos)) {
                     this.scanChunkForHoles(chunk);
                     this.holeScannedChunks.add(chunkPos);
                     Thread.sleep(10L);
                  }
               }
            }
         } catch (InterruptedException var5) {
            Thread.currentThread().interrupt();
         } catch (Exception var6) {
         }

      }
   }

   private void scanChunkForHoles(class_2818 chunk) {
      if (!this.shouldStop && chunk != null && !chunk.method_12223() && !this.isPausedDueToLag) {
         class_1923 chunkPos = chunk.method_12004();
         int xStart = chunkPos.method_8326();
         int zStart = chunkPos.method_8328();
         int yMin = Math.max(chunk.method_31607(), 20);
         int yMax = Math.min(chunk.method_31607() + chunk.method_31605(), 60);

         for(int x = xStart; x < xStart + 16; ++x) {
            for(int z = zStart; z < zStart + 16; ++z) {
               for(int y = yMin; y < yMax; ++y) {
                  if (this.shouldStop || this.isPausedDueToLag) {
                     return;
                  }

                  class_2338 pos = new class_2338(x, y, z);

                  try {
                     this.checkHole1x1(pos);
                     this.checkHole3x1(pos);
                  } catch (Exception var12) {
                  }
               }
            }
         }

      }
   }

   private void checkHole1x1(class_2338 pos) {
      if (this.isValidHoleSection(pos)) {
         class_2339 currentPos = pos.method_25503();

         while(this.isValidHoleSection(currentPos)) {
            currentPos.method_10098(class_2350.field_11036);
         }

         if (currentPos.method_10264() - pos.method_10264() >= 8) {
            class_238 holeBox = new class_238((double)pos.method_10263(), (double)pos.method_10264(), (double)pos.method_10260(), (double)(pos.method_10263() + 1), (double)currentPos.method_10264(), (double)(pos.method_10260() + 1));
            if (!this.holes1x1.contains(holeBox) && this.holes1x1.stream().noneMatch((existingHole) -> {
               return existingHole.method_994(holeBox);
            })) {
               this.holes1x1.add(holeBox);
            }
         }
      }

   }

   private void checkHole3x1(class_2338 pos) {
      class_2339 currentPos;
      class_238 holeBox;
      if (this.isValid3x1HoleSectionX(pos)) {
         currentPos = pos.method_25503();

         while(this.isValid3x1HoleSectionX(currentPos)) {
            currentPos.method_10098(class_2350.field_11036);
         }

         if (currentPos.method_10264() - pos.method_10264() >= 8) {
            holeBox = new class_238((double)pos.method_10263(), (double)pos.method_10264(), (double)pos.method_10260(), (double)(pos.method_10263() + 3), (double)currentPos.method_10264(), (double)(pos.method_10260() + 1));
            if (!this.holes3x1.contains(holeBox) && this.holes3x1.stream().noneMatch((existingHole) -> {
               return existingHole.method_994(holeBox);
            })) {
               this.holes3x1.add(holeBox);
            }
         }
      }

      if (this.isValid3x1HoleSectionZ(pos)) {
         currentPos = pos.method_25503();

         while(this.isValid3x1HoleSectionZ(currentPos)) {
            currentPos.method_10098(class_2350.field_11036);
         }

         if (currentPos.method_10264() - pos.method_10264() >= 8) {
            holeBox = new class_238((double)pos.method_10263(), (double)pos.method_10264(), (double)pos.method_10260(), (double)(pos.method_10263() + 1), (double)currentPos.method_10264(), (double)(pos.method_10260() + 3));
            if (!this.holes3x1.contains(holeBox) && this.holes3x1.stream().noneMatch((existingHole) -> {
               return existingHole.method_994(holeBox);
            })) {
               this.holes3x1.add(holeBox);
            }
         }
      }

   }

   private boolean isValidHoleSection(class_2338 pos) {
      return this.isAirBlock(pos) && !this.isAirBlock(pos.method_10095()) && !this.isAirBlock(pos.method_10072()) && !this.isAirBlock(pos.method_10078()) && !this.isAirBlock(pos.method_10067());
   }

   private boolean isValid3x1HoleSectionX(class_2338 pos) {
      return this.isAirBlock(pos) && this.isAirBlock(pos.method_10078()) && this.isAirBlock(pos.method_10089(2)) && !this.isAirBlock(pos.method_10095()) && !this.isAirBlock(pos.method_10072()) && !this.isAirBlock(pos.method_10089(3)) && !this.isAirBlock(pos.method_10067()) && !this.isAirBlock(pos.method_10078().method_10095()) && !this.isAirBlock(pos.method_10078().method_10072()) && !this.isAirBlock(pos.method_10089(2).method_10095()) && !this.isAirBlock(pos.method_10089(2).method_10072());
   }

   private boolean isValid3x1HoleSectionZ(class_2338 pos) {
      return this.isAirBlock(pos) && this.isAirBlock(pos.method_10072()) && this.isAirBlock(pos.method_10077(2)) && !this.isAirBlock(pos.method_10078()) && !this.isAirBlock(pos.method_10067()) && !this.isAirBlock(pos.method_10077(3)) && !this.isAirBlock(pos.method_10095()) && !this.isAirBlock(pos.method_10072().method_10078()) && !this.isAirBlock(pos.method_10072().method_10067()) && !this.isAirBlock(pos.method_10077(2).method_10078()) && !this.isAirBlock(pos.method_10077(2).method_10067());
   }

   private boolean isAirBlock(class_2338 pos) {
      return this.mc.field_1687 == null ? false : this.mc.field_1687.method_8320(pos).method_26215();
   }

   private void cleanupDistantHoles() {
      if (this.mc.field_1724 != null) {
         int viewDist = (Integer)this.mc.field_1690.method_42503().method_41753();
         int playerChunkX = (int)this.mc.field_1724.method_23317() / 16;
         int playerChunkZ = (int)this.mc.field_1724.method_23321() / 16;
         this.holes1x1.removeIf((holeBox) -> {
            int holeChunkX = (int)Math.floor(holeBox.method_1005().method_10216()) / 16;
            int holeChunkZ = (int)Math.floor(holeBox.method_1005().method_10215()) / 16;
            int dx = Math.abs(holeChunkX - playerChunkX);
            int dz = Math.abs(holeChunkZ - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
         this.holes3x1.removeIf((holeBox) -> {
            int holeChunkX = (int)Math.floor(holeBox.method_1005().method_10216()) / 16;
            int holeChunkZ = (int)Math.floor(holeBox.method_1005().method_10215()) / 16;
            int dx = Math.abs(holeChunkX - playerChunkX);
            int dz = Math.abs(holeChunkZ - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
         this.holeScannedChunks.removeIf((chunkPos) -> {
            int dx = Math.abs(chunkPos.field_9181 - playerChunkX);
            int dz = Math.abs(chunkPos.field_9180 - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
      }
   }

   private void scanChunksBackground() {
      if (!this.shouldStop && this.mc.field_1687 != null && this.mc.field_1724 != null && !this.isPausedDueToLag) {
         try {
            List<class_2818> loadedChunks = this.getLoadedChunks();
            Iterator var2 = loadedChunks.iterator();

            while(var2.hasNext()) {
               class_2818 chunk = (class_2818)var2.next();
               if (!this.shouldStop && chunk != null && !chunk.method_12223() && !this.isPausedDueToLag) {
                  class_1923 chunkPos = chunk.method_12004();
                  if (!this.scannedChunks.contains(chunkPos)) {
                     boolean wasAlreadyFlagged = this.flaggedChunks.contains(chunkPos);
                     Set<class_2338> chunkFlaggedBlocks = this.scanChunkForCoveredOres(chunk);
                     boolean shouldFlag = !chunkFlaggedBlocks.isEmpty();
                     if (shouldFlag) {
                        this.flaggedChunks.add(chunkPos);
                        this.flaggedBlocks.put(chunkPos, chunkFlaggedBlocks);
                        if (!wasAlreadyFlagged && !this.notifiedChunks.contains(chunkPos)) {
                           this.notifyChunkFound(chunkPos);
                           this.notifiedChunks.add(chunkPos);
                        }
                     } else {
                        this.flaggedChunks.remove(chunkPos);
                        this.notifiedChunks.remove(chunkPos);
                        this.flaggedBlocks.remove(chunkPos);
                     }

                     this.scannedChunks.add(chunkPos);
                     Thread.sleep(5L);
                  }
               }
            }
         } catch (InterruptedException var8) {
            Thread.currentThread().interrupt();
         } catch (Exception var9) {
         }

      }
   }

   private void notifyChunkFound(class_1923 chunkPos) {
      this.mc.execute(() -> {
         try {
            if (this.mc.field_1724 != null) {
               this.mc.field_1724.method_5783(class_3417.field_26979, 1.0F, 1.0F);
            }

            int worldX = chunkPos.field_9181 * 16 + 8;
            int worldZ = chunkPos.field_9180 * 16 + 8;
            ChunkFinder.ChunkFinderToast toast = new ChunkFinder.ChunkFinderToast(String.valueOf(worldX), String.valueOf(worldZ));
            this.mc.method_1566().method_1999(toast);
         } catch (Exception var5) {
         }

      });
   }

   private Set<class_2338> scanChunkForCoveredOres(class_2818 chunk) {
      Set<class_2338> foundBlocks = ConcurrentHashMap.newKeySet();
      int pistonCount = 0;
      if (!this.shouldStop && chunk != null && !chunk.method_12223() && !this.isPausedDueToLag) {
         class_1923 chunkPos = chunk.method_12004();
         int xStart = chunkPos.method_8326();
         int zStart = chunkPos.method_8328();
         int yMin = Math.max(chunk.method_31607(), 20);
         int yMax = Math.min(chunk.method_31607() + chunk.method_31605(), 60);

         int x;
         int z;
         int sameCount;
         class_2338 pos;
         class_2680 state;
         class_2248 block;
         for(x = xStart; x < xStart + 16; ++x) {
            for(z = zStart; z < zStart + 16; ++z) {
               for(sameCount = yMin; sameCount < yMax; ++sameCount) {
                  if (this.shouldStop || this.isPausedDueToLag) {
                     return foundBlocks;
                  }

                  pos = new class_2338(x, sameCount, z);

                  try {
                     state = chunk.method_8320(pos);
                     block = state.method_26204();
                     if ((block == class_2246.field_27165 || block == class_2246.field_28888) && sameCount > 20 && sameCount < 60 && this.isBlockCovered(chunk, pos) && this.isPositionUnderground(pos)) {
                        foundBlocks.add(pos);
                     }

                     if (this.isTargetBlock(state) && sameCount >= 20 && sameCount <= 60 && this.isBlockCovered(chunk, pos) && this.isPositionUnderground(pos)) {
                        foundBlocks.add(pos);
                     }

                     if (this.isRotatedDeepslate(state) && this.isBlockCovered(chunk, pos) && this.isPositionUnderground(pos)) {
                        foundBlocks.add(pos);
                     }

                     if (block == class_2246.field_10560 || block == class_2246.field_10615) {
                        ++pistonCount;
                     }
                  } catch (Exception var22) {
                  }
               }
            }
         }

         for(x = xStart; x < xStart + 16; ++x) {
            for(z = zStart; z < zStart + 16; ++z) {
               sameCount = 0;
               class_2248 plugType = null;
               List<class_2338> currentPlug = new ArrayList();

               for(int y = yMin; y <= yMax; ++y) {
                  class_2338 pos = new class_2338(x, y, z);
                  class_2680 state = chunk.method_8320(pos);
                  class_2248 block = state.method_26204();
                  if (!this.isPlugBlock(block)) {
                     sameCount = 0;
                     plugType = null;
                     currentPlug.clear();
                  } else {
                     if (plugType != null && block != plugType) {
                        sameCount = 1;
                        plugType = block;
                        currentPlug.clear();
                        currentPlug.add(pos);
                     } else {
                        ++sameCount;
                        plugType = block;
                        currentPlug.add(pos);
                     }

                     if (sameCount >= 25 && this.isCoveredColumn(chunk, x, z, y - sameCount + 1, y, plugType)) {
                        List<class_2338> verifiedBlocks = new ArrayList();
                        Iterator var19 = currentPlug.iterator();

                        while(var19.hasNext()) {
                           class_2338 plugPos = (class_2338)var19.next();
                           if (this.isBlockCovered(chunk, plugPos) && this.isPositionUnderground(plugPos)) {
                              verifiedBlocks.add(plugPos);
                           }
                        }

                        foundBlocks.addAll(verifiedBlocks);
                     }
                  }
               }
            }
         }

         if (pistonCount >= 5) {
            for(x = xStart; x < xStart + 16; ++x) {
               for(z = zStart; z < zStart + 16; ++z) {
                  for(sameCount = yMin; sameCount < yMax && !this.shouldStop && !this.isPausedDueToLag; ++sameCount) {
                     pos = new class_2338(x, sameCount, z);

                     try {
                        state = chunk.method_8320(pos);
                        block = state.method_26204();
                        if (block == class_2246.field_10560 || block == class_2246.field_10615) {
                           foundBlocks.add(pos);
                        }
                     } catch (Exception var21) {
                     }
                  }
               }
            }
         }

         return foundBlocks;
      } else {
         return foundBlocks;
      }
   }

   private boolean isPlugBlock(class_2248 block) {
      return block == class_2246.field_10474 || block == class_2246.field_10115 || block == class_2246.field_10566 || block == class_2246.field_10508;
   }

   private boolean isCoveredColumn(class_2818 chunk, int x, int z, int yStart, int yEnd, class_2248 blockType) {
      for(int y = yStart; y <= yEnd; ++y) {
         class_2338 pos = new class_2338(x, y, z);
         if (!chunk.method_8320(pos).method_27852(blockType)) {
            return false;
         }

         class_2350[] var9 = class_2350.values();
         int var10 = var9.length;

         for(int var11 = 0; var11 < var10; ++var11) {
            class_2350 dir = var9[var11];
            class_2338 adj = pos.method_10093(dir);
            class_2680 adjState = chunk.method_8320(adj);
            if (adjState.method_26215() || !adjState.method_26212(this.mc.field_1687, adj)) {
               return false;
            }
         }
      }

      return true;
   }

   private boolean isPositionUnderground(class_2338 pos) {
      if (this.mc.field_1687 == null) {
         return false;
      } else {
         int checkHeight = Math.min(pos.method_10264() + 50, 80);
         int solidBlocksAbove = 0;

         for(int y = pos.method_10264() + 1; y <= checkHeight; ++y) {
            class_2338 checkPos = new class_2338(pos.method_10263(), y, pos.method_10260());
            class_2680 state = this.mc.field_1687.method_8320(checkPos);
            if (!state.method_26215() && state.method_26212(this.mc.field_1687, checkPos)) {
               ++solidBlocksAbove;
            }
         }

         return solidBlocksAbove >= 3;
      }
   }

   private boolean isTargetBlock(class_2680 state) {
      if (state != null && !state.method_26215()) {
         class_2248 block = state.method_26204();
         if (block == class_2246.field_28888) {
            return !this.isRotatedDeepslate(state);
         } else {
            return block == class_2246.field_27165;
         }
      } else {
         return false;
      }
   }

   private boolean isRotatedDeepslate(class_2680 state) {
      if (state != null && !state.method_26215()) {
         class_2248 block = state.method_26204();
         if (block == class_2246.field_28888 && state.method_28498(class_2465.field_11459)) {
            class_2351 axis = (class_2351)state.method_11654(class_2465.field_11459);
            return axis == class_2351.field_11048 || axis == class_2351.field_11051;
         } else {
            return false;
         }
      } else {
         return false;
      }
   }

   private boolean isBlockCovered(class_2818 chunk, class_2338 pos) {
      class_2350[] directions = new class_2350[]{class_2350.field_11036, class_2350.field_11033, class_2350.field_11043, class_2350.field_11035, class_2350.field_11034, class_2350.field_11039};
      class_2350[] var4 = directions;
      int var5 = directions.length;

      for(int var6 = 0; var6 < var5; ++var6) {
         class_2350 dir = var4[var6];
         class_2338 adjacentPos = pos.method_10093(dir);

         try {
            class_2680 adjacentState = null;
            if (this.mc.field_1687 != null) {
               adjacentState = this.mc.field_1687.method_8320(adjacentPos);
               if (!adjacentState.method_26215() && !this.isTransparentBlock(adjacentState)) {
                  if (adjacentState.method_26212(this.mc.field_1687, adjacentPos)) {
                     continue;
                  }

                  return false;
               }

               return false;
            }

            return false;
         } catch (Exception var10) {
            return false;
         }
      }

      return true;
   }

   private boolean isTransparentBlock(class_2680 state) {
      class_2248 block = state.method_26204();
      if (block != class_2246.field_10033 && block != class_2246.field_10382 && block != class_2246.field_10164 && block != class_2246.field_10295 && block != class_2246.field_10225 && block != class_2246.field_10384) {
         if (block != class_2246.field_10087 && block != class_2246.field_10227 && block != class_2246.field_10574 && block != class_2246.field_10271 && block != class_2246.field_10049 && block != class_2246.field_10157 && block != class_2246.field_10317 && block != class_2246.field_10555 && block != class_2246.field_9996 && block != class_2246.field_10248 && block != class_2246.field_10399 && block != class_2246.field_10060 && block != class_2246.field_10073 && block != class_2246.field_10357 && block != class_2246.field_10272 && block != class_2246.field_9997 && block != class_2246.field_27115) {
            return block == class_2246.field_10503 || block == class_2246.field_9988 || block == class_2246.field_10539 || block == class_2246.field_10335 || block == class_2246.field_10098 || block == class_2246.field_10035 || block == class_2246.field_37551 || block == class_2246.field_42731;
         } else {
            return true;
         }
      } else {
         return true;
      }
   }

   private void cleanupDistantChunks() {
      if (this.mc.field_1724 != null) {
         int viewDist = (Integer)this.mc.field_1690.method_42503().method_41753();
         int playerChunkX = (int)this.mc.field_1724.method_23317() / 16;
         int playerChunkZ = (int)this.mc.field_1724.method_23321() / 16;
         this.flaggedChunks.removeIf((chunkPos) -> {
            int dx = Math.abs(chunkPos.field_9181 - playerChunkX);
            int dz = Math.abs(chunkPos.field_9180 - playerChunkZ);
            boolean shouldRemove = dx > viewDist || dz > viewDist;
            if (shouldRemove) {
               this.flaggedBlocks.remove(chunkPos);
            }

            return shouldRemove;
         });
         this.scannedChunks.removeIf((chunkPos) -> {
            int dx = Math.abs(chunkPos.field_9181 - playerChunkX);
            int dz = Math.abs(chunkPos.field_9180 - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
         this.notifiedChunks.removeIf((chunkPos) -> {
            int dx = Math.abs(chunkPos.field_9181 - playerChunkX);
            int dz = Math.abs(chunkPos.field_9180 - playerChunkZ);
            return dx > viewDist || dz > viewDist;
         });
      }
   }

   @EventListener
   public void onRender3D(Render3DEvent event) {
      if (this.mc.field_1724 != null && this.mc.field_1687 != null) {
         if (!this.killSwitchActivated) {
            class_4184 cam = RenderUtils.getCamera();
            if (cam != null) {
               class_243 camPos = RenderUtils.getCameraPos();
               class_4587 matrices = event.matrixStack;
               matrices.method_22903();
               matrices.method_22907(class_7833.field_40714.rotationDegrees(cam.method_19329()));
               matrices.method_22907(class_7833.field_40716.rotationDegrees(cam.method_19330() + 180.0F));
               matrices.method_22904(-camPos.field_1352, -camPos.field_1351, -camPos.field_1350);
            }

            if (!this.flaggedChunks.isEmpty()) {
               Iterator var6 = this.flaggedChunks.iterator();

               while(var6.hasNext()) {
                  class_1923 chunkPos = (class_1923)var6.next();
                  Objects.requireNonNull(this);
                  int a = 120;
                  this.renderChunkHighlight(event.matrixStack, chunkPos, new Color(0, 255, 0, a));
                  this.renderFlaggedBlocks(event.matrixStack, chunkPos);
               }
            }

            if (this.holeESP.getValue()) {
               if (!this.holes1x1.isEmpty()) {
                  this.renderHoles1x1(event.matrixStack);
               }

               if (!this.holes3x1.isEmpty()) {
                  this.renderHoles3x1(event.matrixStack);
               }
            }

            event.matrixStack.method_22909();
         }
      }
   }

   private void renderHoles1x1(class_4587 stack) {
      Color color = this.hole1x1Color;
      Iterator var3 = this.holes1x1.iterator();

      while(var3.hasNext()) {
         class_238 hole = (class_238)var3.next();
         RenderUtils.renderFilledBox(stack, (float)hole.field_1323, (float)hole.field_1322, (float)hole.field_1321, (float)hole.field_1320, (float)hole.field_1325, (float)hole.field_1324, color);
         this.renderBoxOutline(stack, (float)hole.field_1323, (float)hole.field_1322, (float)hole.field_1321, (float)hole.field_1320, (float)hole.field_1325, (float)hole.field_1324, new Color(color.getRed(), color.getGreen(), color.getBlue(), 255));
      }

   }

   private void renderHoles3x1(class_4587 stack) {
      Color color = this.hole3x1Color;
      Iterator var3 = this.holes3x1.iterator();

      while(var3.hasNext()) {
         class_238 hole = (class_238)var3.next();
         RenderUtils.renderFilledBox(stack, (float)hole.field_1323, (float)hole.field_1322, (float)hole.field_1321, (float)hole.field_1320, (float)hole.field_1325, (float)hole.field_1324, color);
         this.renderBoxOutline(stack, (float)hole.field_1323, (float)hole.field_1322, (float)hole.field_1321, (float)hole.field_1320, (float)hole.field_1325, (float)hole.field_1324, new Color(color.getRed(), color.getGreen(), color.getBlue(), 255));
      }

   }

   private void renderChunkHighlight(class_4587 stack, class_1923 chunkPos, Color renderColor) {
      int startX = chunkPos.field_9181 * 16;
      int startZ = chunkPos.field_9180 * 16;
      int endX = startX + 16;
      int endZ = startZ + 16;
      double y = 60.0D;
      double height = 0.10000000149011612D;
      class_238 chunkBox = new class_238((double)startX, y, (double)startZ, (double)endX, y + height, (double)endZ);
      RenderUtils.renderFilledBox(stack, (float)chunkBox.field_1323, (float)chunkBox.field_1322, (float)chunkBox.field_1321, (float)chunkBox.field_1320, (float)chunkBox.field_1325, (float)chunkBox.field_1324, renderColor);
      this.renderBoxOutline(stack, (float)chunkBox.field_1323, (float)chunkBox.field_1322, (float)chunkBox.field_1321, (float)chunkBox.field_1320, (float)chunkBox.field_1325, (float)chunkBox.field_1324, renderColor);
   }

   private void renderFlaggedBlocks(class_4587 stack, class_1923 chunkPos) {
      Set<class_2338> blocks = (Set)this.flaggedBlocks.get(chunkPos);
      if (blocks != null && !blocks.isEmpty()) {
         new Color(255, 0, 0, 120);
         Iterator var5 = blocks.iterator();

         while(var5.hasNext()) {
            class_2338 blockPos = (class_2338)var5.next();
            new class_238(blockPos);
         }

      }
   }

   private List<class_2818> getLoadedChunks() {
      List<class_2818> chunks = new ArrayList();
      int viewDist = (Integer)this.mc.field_1690.method_42503().method_41753();

      for(int x = -viewDist; x <= viewDist; ++x) {
         for(int z = -viewDist; z <= viewDist; ++z) {
            class_2818 chunk = this.mc.field_1687.method_2935().method_21730((int)this.mc.field_1724.method_23317() / 16 + x, (int)this.mc.field_1724.method_23321() / 16 + z);
            if (chunk != null) {
               chunks.add(chunk);
            }
         }
      }

      return chunks;
   }

   private void renderBoxOutline(class_4587 stack, float minX, float minY, float minZ, float maxX, float maxY, float maxZ, Color color) {
      class_243[] corners = new class_243[]{new class_243((double)minX, (double)minY, (double)minZ), new class_243((double)maxX, (double)minY, (double)minZ), new class_243((double)maxX, (double)minY, (double)maxZ), new class_243((double)minX, (double)minY, (double)maxZ), new class_243((double)minX, (double)maxY, (double)minZ), new class_243((double)maxX, (double)maxY, (double)minZ), new class_243((double)maxX, (double)maxY, (double)maxZ), new class_243((double)minX, (double)maxY, (double)maxZ)};
      RenderUtils.renderLine(stack, color, corners[0], corners[1]);
      RenderUtils.renderLine(stack, color, corners[1], corners[2]);
      RenderUtils.renderLine(stack, color, corners[2], corners[3]);
      RenderUtils.renderLine(stack, color, corners[3], corners[0]);
      RenderUtils.renderLine(stack, color, corners[4], corners[5]);
      RenderUtils.renderLine(stack, color, corners[5], corners[6]);
      RenderUtils.renderLine(stack, color, corners[6], corners[7]);
      RenderUtils.renderLine(stack, color, corners[7], corners[4]);
      RenderUtils.renderLine(stack, color, corners[0], corners[4]);
      RenderUtils.renderLine(stack, color, corners[1], corners[5]);
      RenderUtils.renderLine(stack, color, corners[2], corners[6]);
      RenderUtils.renderLine(stack, color, corners[3], corners[7]);
   }

   public static class ChunkFinderToast implements class_368 {
      private final String chunkX;
      private final String chunkZ;
      private long startTime;
      private boolean hasPlayed = false;
      private static final long DISPLAY_TIME = 3000L;

      public ChunkFinderToast(String chunkX, String chunkZ) {
         this.chunkX = chunkX;
         this.chunkZ = chunkZ;
      }

      public class_369 method_1986(class_332 context, class_374 manager, long startTime) {
         if (this.startTime == 0L) {
            this.startTime = startTime;
         }

         if (!this.hasPlayed) {
            manager.method_1995().method_1483().method_4873(class_1109.method_4757(class_3417.field_26979, 1.0F, 1.0F));
            this.hasPlayed = true;
         }

         context.method_52706(class_2960.method_60656("toast/advancement"), 0, 0, this.method_29049(), this.method_29050());
         context.method_51445(new class_1799(class_1802.field_8106), 6, 6);
         class_2561 title = class_2561.method_43470("ChunkFinder");
         context.method_51439(manager.method_1995().field_1772, title, 30, 7, 16586941, false);
         String var10000 = this.chunkX;
         class_2561 coords = class_2561.method_43470("X: " + var10000 + " Z: " + this.chunkZ);
         context.method_51439(manager.method_1995().field_1772, coords, 30, 18, 16777215, false);
         return startTime - this.startTime < 3000L ? class_369.field_2210 : class_369.field_2209;
      }

      public Object method_1987() {
         return ChunkFinder.ChunkFinderToast.Type.INSTANCE;
      }

      public int method_29049() {
         return 160;
      }

      public int method_29050() {
         return 32;
      }

      public static class Type {
         public static final ChunkFinder.ChunkFinderToast.Type INSTANCE = new ChunkFinder.ChunkFinderToast.Type();
      }
   }
}
