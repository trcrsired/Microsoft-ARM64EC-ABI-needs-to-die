# Why Microsoft ARM64EC/ARM64X ABI MUST DIE

**What is ARM64EC?**

The ARM64EC ABI (Application Binary Interface) introduced by Microsoft has garnered its fair share of criticism and rightfully so. Here are some reasons why this ABI is problematic and why it MUST die.
1. **Performance Concerns**
   Microsoft claims that ARM64EC offers great performance, but in practice, it falls short. Unlike the native AArch64 ABI (which Microsoft refers to as the "classic ARM ABI"), certain C++ types like `std::span` and `std::string_view` are passed by memory instead of registers. This results in a performance penalty for writing C++ code. You can see my previous discussion on this here: [Link to Discussion](https://developercommunity.visualstudio.com/t/std::span-is-not-zero-cost-because-of-th/1429284). Additionally, Microsoft wastes General Purpose Registers (GPRs) on x87. And it helps nothing, 32-bit x86 software runs extremely slowly on AArch64 machines. According to Geekerwan, 32-bit x86 software loses 90% of its performance due to translation on Snapdragon X Elite machines. You can see more about this here: [Geekerwan on YouTube](https://youtu.be/Vq5g9a_CsRo?feature=shared&t=1429).

3. **Lack of Compiler and Library Support**
   ARM64EC does not provide significant benefits to compilers or library writers. Neither LLVM nor GCC fully support it correctly. For example, the `fast_io` library, which has long supported the native AArch64 Windows ABI and has been well-tested with Wine on Android and Raspberry Pi, had to rewrite 10,000 lines of its 80,000-line codebase just to support ARM64EC. This is a substantial and unjustifiable overhead. You can see the related commit here: [fast_io Commit](https://github.com/cppfastio/fast_io/commit/ff6156fbd78d86879d07b8411d201869037e74db).

4. **No Universal Binaries**
   Unlike Apple, which provides universal binaries, Microsoft refuses to do the same. The ARM64X PE binary does not work on x86_64 machines, making it essentially useless. ARM64X is not a universal binary; it is just Microsoft's attempt to mix ARM64EC and native AArch64 binaries inside a single binary.

5. **Breaking Forward Compatibility**
   Microsoft has a one-to-one mapping of registers from x86_64 to ARM64EC. This leads to significant forward compatibility issues. For instance, binaries compiled with the `-mapxf` flag using GCC and Clang today do not work on ARM64EC. This implies that future x86_64 executables will never run on AArch64 Windows machines with translation, even though there is theoretically nothing preventing this. More details on this can be found here: [Register Mapping](https://github.com/MicrosoftDocs/cpp-docs/blob/main/docs/build/arm64ec-windows-abi-conventions.md#register-mapping).

6. **Intel APX Overview**
   Intel's Advanced Performance Extensions (Intel APX) is a significant update to the x86 instruction set architecture. It doubles the number of general-purpose registers (GPRs) from 16 to 32, allowing the compiler to keep more values in registers. This results in fewer loads and stores, improving performance and reducing power consumption. Intel APX also introduces new features that enhance general-purpose performance without significantly increasing silicon area or power consumption.

7. **Impact on Workloads**
   The performance features introduced by Intel APX will have a limited impact on workloads that suffer from a large number of conditional branch mispredictions. However, the overall performance gains across various workloads are expected to be substantial.

8. **Compatibility Issues with ARM64EC**
   The one-to-one mapping of registers in ARM64EC breaks forward compatibility with Intel APX. The new GPRs introduced by Intel APX have no corresponding mapping in ARM64EC, leading to significant compatibility issues. This further complicates the development and maintenance of software across different architectures.

9. **Encourages Poor Software Practices**
   The ARM64EC ABI encourages subpar software practices by not promoting native ARM support. For example, frameworks like Qt often do not support ARM natively, causing many applications that rely on Qt to fail to compile for ARM. Even when they do compile, the performance penalty remains significant for the reasons previously discussed.

10. **Missteps with Windows RT**
   The failure of Windows RT had nothing to do with the ARM ABI. Microsoft attempted to create a walled garden by banning native ARM Win32 executables from running on Surface RT, which backfired because Windows users generally reject walled gardens. This strategic misstep contributed significantly to the downfall of Windows RT.

n conclusion, ARM64EC introduces performance penalties, lacks comprehensive support from compilers and libraries, does not facilitate the creation of universal binaries, and breaks forward compatibility. These issues, combined with the compatibility problems with Intel APX, make it clear that ARM64EC is not a viable ABI and must be abandoned entirely.

Windows needs universal binaries like Apple does. It just shows you Microsoft is just objectively terrible for doing just basic stuff. No wonder many users want to use Linux instead so they do not need to deal with dramas like this. ARM64EC must die.
