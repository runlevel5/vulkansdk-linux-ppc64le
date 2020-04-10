# How to build vulkansdk-linux-ppc64le

The official vulkansdk tarball does not comes with binaries for ppc64le platform. I have lodged a [request upstream](https://vulkan.lunarg.com/issue/view/5e8fe31286da81205ba4b137). While waiting, you could compile the binaries yourself:


```
# Please use Ubuntu Bionic ppc64le
export VK_VERSION=1.2.131.2
export VULKAN_SDK_SOURCE=$(pwd)/$VK_VERSION/source
export VULKAN_SDK=$TRAVIS_BUILD_DIR/$VK_VERSION/ppc64le
sudo apt-get -qq update
sudo apt-get install -y make gcc g++ mesa-common-dev libglu1-mesa-dev libxxf86dga-dev libxxf86vm-dev libasound2-dev libx11-dev libxcb1-dev libwayland-dev cmake git build-essential bison libx11-xcb-dev libxkbcommon-dev libwayland-dev libxrandr-dev libxcb-randr0-dev wget autotools-dev libxcb-keysyms1 libxcb-keysyms1-dev libxcb-ewmh-dev
curl -L -v -o vulkansdk-linux-ppc64le-$VK_VERSION.tar.gz -O https://sdk.lunarg.com/sdk/download/$VK_VERSION/linux/vulkansdk-linux-ppc64le-$VK_VERSION.tar.gz?Human=true
tar zxf vulkansdk-linux-ppc64le-$VK_VERSION.tar.gz
mkdir -p $VULKAN_SDK
rm -rf $VULKAN_SDK_SOURCE/x86_64
cd $VULKAN_SDK_SOURCE/glslang && cmake -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK . && make install && make clean
cd $VULKAN_SDK_SOURCE/Vulkan-Headers && cmake -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK . && make install && make clean
cd $VULKAN_SDK_SOURCE/Vulkan-Tools && cmake -DBUILD_CUBE=OFF -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK . && make install && make clean
cd $VULKAN_SDK_SOURCE/SPIRV-Cross && mkdir build && cd build && cmake -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK -DSPIRV_CROSS_ENABLE_TESTS=off .. && make install && make clean
cd $VULKAN_SDK_SOURCE/Vulkan-ValidationLayers && cmake -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK -DGLSLANG_INSTALL_DIR=$VULKAN_SDK/bin -DGLSLANG_SPIRV_INCLUDE_DIR=$VULKAN_SDK/include/glslang -DSPIRV_TOOLS_INCLUDE_DIR=$VULKAN_SDK/include . && make install && cp ./layers/*.h $VULKAN_SDK/include/vulkan/ && cp ./layers/generated/*.h $VULKAN_SDK/include/vulkan/
cd $VULKAN_SDK_SOURCE/Vulkan-Loader && mkdir build && cd build && cmake -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK -DVULKAN_HEADERS_INSTALL_DIR=$VULKAN_SDK .. && make install && cp ../loader/*.h $VULKAN_SDK/include/vulkan/ && cp ../loader/generated/*.h $VULKAN_SDK/include/vulkan/ && make clean
cd $VULKAN_SDK_SOURCE/layer_factory && sed -i 's/x86_64/ppc64le/g' CMakeLists.txt && cmake -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK . && make && make clean
cd $VULKAN_SDK_SOURCE/shaderc && ./update_shaderc_sources.py && cd ./src && cmake -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK . && make install
cd $VULKAN_SDK_SOURCE/VulkanTools && ./update_external_sources.sh && cmake -DCMAKE_INSTALL_PREFIX=$VULKAN_SDK -DVULKAN_VALIDATIONLAYERS_INSTALL_DIR=$VULKAN_SDK -DVULKAN_HEADERS_INSTALL_DIR=$VULKAN_SDK . && make install
```
